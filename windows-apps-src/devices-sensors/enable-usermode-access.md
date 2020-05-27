---
title: Включение доступа в пользовательском режиме к GPIO, I2C и SPI
description: В этом учебнике описывается, как включить доступ в пользовательском режиме к GPIO, I2C, SPI и UART в Windows 10.
ms.date: 02/08/2017
ms.topic: article
keywords: Windows 10, uwp, acpi, gpio, i2c, spi, uefi
ms.assetid: 2fbdfc78-3a43-4828-ae55-fd3789da7b34
ms.localizationpriority: medium
ms.openlocfilehash: eb8f4cf619e2e2b3fe089ce9478ae61315d1c13e
ms.sourcegitcommit: e51f9489d8c977c3498afb1a75c91f96ac3a642b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/26/2020
ms.locfileid: "83854770"
---
# <a name="enable-user-mode-access-to-gpio-i2c-and-spi"></a>Включение доступа в пользовательском режиме к GPIO, I2C и SPI

Windows 10 содержит новые интерфейсы API для прямого доступа из пользовательского режима общего назначения (GPIO), канала связи (I2C), интерфейса Serial периферийных устройств (SPI) и универсального асинхронного приемопередатчика (UART). Такие доски разработки, как Raspberry Pi 2, предоставляют подмножество этих подключений, что позволяет расширить базовый модуль вычислений с помощью настраиваемой цепи для решения определенного приложения. Эти низкоуровневые шины обычно используются с другими ключевыми функциями платы, и лишь некоторые контакты и шины GPIO предоставляются в заголовках. Чтобы сохранить стабильность системы, необходимо указать, какие контакты и шины могут быть защищены для изменения приложениями пользовательского режима.

В этом документе описывается, как указать эту конфигурацию в расширенном интерфейсе конфигурации и Power Interface (ACPI), а также средства для проверки правильности указания конфигурации.

> [!IMPORTANT]
> Аудитория для этого документа — Единый интерфейс EFI (UEFI) и разработчики ACPI. Предполагается, что вы знакомы с ACPI, созданием исходного языка ACPI (АСЛ) и Спбккс/Гпиоклкс.

Доступ в пользовательском режиме к шинам низкого уровня в Windows осуществляется через существующие `GpioClx` платформы и `SpbCx` . Новый драйвер под названием *рхпрокси*, доступный в Windows IOT базовая и Windows Enterprise, предоставляет доступ `GpioClx` `SpbCx` к ресурсам и в пользовательском режиме. Чтобы включить API, узел устройства для рхпрокси должен быть объявлен в таблицах ACPI с каждым из ресурсов GPIO и SPB, которые должны быть доступны в пользовательском режиме. Этот документ содержит пошаговое руководство по созданию и проверке ASL.

## <a name="asl-by-example"></a>ASL на примерах

Давайте рассмотрим объявление узла устройств rhproxy в Raspberry Pi 2. Сначала создайте объявление устройства ACPI в \\ области _SB.

```cpp
Device(RHPX)
{
    Name(_HID, "MSFT8000")
    Name(_CID, "MSFT8000")
    Name(_UID, 1)
}
```

- _HID – код оборудования. Укажите заданный поставщиком код оборудования.
- _CID – совместимый код. Должен быть равен “MSFT8000”.
- _UID – уникальный код. Задайте его равным 1.

Далее мы объявляем все ресурсы GPIO и SPB, которые должны быть доступны в пользовательском режиме. Порядок объявления ресурсов важен, потому что индексированные ресурсы используются для связи свойств с ресурсами. Если предоставлено несколько шин I2C или SPI, первая объявленная шина считается шиной по умолчанию для этого типа и представляет собой экземпляр, возвращаемый методами `GetDefaultAsync()`[Windows.Devices.I2c.I2cController](https://docs.microsoft.com/uwp/api/windows.devices.i2c.i2ccontroller) и [Windows.Devices.Spi.SpiController](https://docs.microsoft.com/uwp/api/windows.devices.spi.spicontroller).

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

- DeviceSelection
- DeviceSelectionPolarity
- WireMode
- SlaveMode
- ResourceSource

Следующие поля являются заполнителями для значений, заданных пользователем во время выполнения:

- DataBitLength
- ConnectionSpeed
- ClockPolarity
- ClockPhase

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

- Необходимо использовать `SpbCx` или быть совместимым с SpbCx
- Передача [тестов MITT SPI](https://docs.microsoft.com/windows-hardware/drivers/spb/spi-tests-in-mitt)
- Поддержка тактовой частоты 400 МГц
- Поддержка 8-битной длины данных
- Поддержка всех режимов SPI: 0, 1, 2, 3

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

- SlaveMode
- ResourceSource

Следующие поля являются заполнителями для значений, заданных пользователем во время выполнения.

- SlaveAddress
- ConnectionSpeed
- AddressingMode

#### <a name="i2c-driver-requirements"></a>Требования драйвера I2C

- Необходимо использовать SpbCx или быть совместимым с SpbCx
- Передача [тестов MITT I2C](https://docs.microsoft.com/windows-hardware/drivers/spb/run-mitt-tests-for-an-i2c-controller-)
- Поддержка 7-битной адресации
- Поддержка тактовой частоты 100 кГц
- Поддержка тактовой частоты 400 кГц

### <a name="gpio"></a>GPIO

Далее мы объявляем все контакты GPIO, доступные в пользовательском режиме. Мы предлагаем следующие рекомендации по выбору контактов для предоставления.

- Объявите все контакты в качестве предоставленных заголовков.
- Объявите контакты, подключенные к полезным функциям системы, таким как кнопки и светодиодные индикаторы.
- Не объявляйте контакты, которые зарезервированы для системных функций и ни к чему не подключены.

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

- Поддерживаются только контроллеры GPIO с сопоставлением памяти. Контроллеры GPIO с интерфейсами в I2C/SPI не поддерживаются. Драйвер контроллера — это контроллер с сопоставлением памяти, если он задает флаг [MemoryMappedController](https://docs.microsoft.com/windows-hardware/drivers/ddi/content/gpioclx/ns-gpioclx-_controller_attribute_flags) в структуре [CLIENT_CONTROLLER_BASIC_INFORMATION](https://docs.microsoft.com/windows-hardware/drivers/ddi/content/gpioclx/ns-gpioclx-_client_controller_basic_information) в ответ на обратный вызов [CLIENT_QueryControllerBasicInformation](https://docs.microsoft.com/windows-hardware/drivers/ddi/content/gpioclx/nc-gpioclx-gpio_client_query_controller_basic_information).
- Каждый контакт требует обоих ресурсов: GpioIO и GpioInt. Ресурс GpioInt должен немедленно следовать за ресурсом GpioIO и иметь тот же номер контакта.
- Ресурсы GPIO нужно упорядочить по увеличению номера контакта.
- Каждый ресурс GpioIO и GpioInt должен содержать ровно один номер контакта в списке контактов.
- Поле ShareType обоих дескрипторов должно быть общим
- Поле EdgeLevel дескриптора GpioInt должно иметь вид Edge
- Поле ActiveLevel дескриптора GpioInt должно иметь вид ActiveBoth
- Поле PinConfig
  - Должны быть одинаковым в дескрипторах GpioIO и GpioInt
  - Должно относиться к одному из следующих объектов: PullUp, PullDown или PullNone. Использовать PullDefault невозможно.
  - Конфигурация запроса должна соответствовать включенному состоянию системы. Помещение контакта в определенный режим опроса из состояния включения не должно менять состояние контакта. Например, если в таблице определено, что контакт функционирует в восходящем режиме, задайте для свойства PinConfig значение PullUp.

Встроенное ПО, UEFI и код инициализации драйверов не должны изменять состояние контакта по сравнению с включенным состоянием во время загрузки. Только пользователь знает, что подключено к контакту и какие изменения состояния безопасны. Состояние включения каждого контакта необходимо зафиксировать документально, чтобы можно было создать оборудование, которое бы исправно функционировало с этим контактом. Состояние контакта не должно неожиданно меняться во время загрузки.

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

- Последовательная нумерация контактов: пользователи видят номера 0, 1, 2..., до суммарного числа предоставленных контактов. 0 — это первый ресурс GpioIo, объявленный в ASL, 1 — второй ресурс GpioIo, объявленный в ASL и т. д.
- Собственная нумерация ПИН-кодов — пользователи видят PIN-коды, указанные в дескрипторах Гпиоио, например 4, 5, 12, 13,...

```cpp
Package (2) { “GPIO-UseDescriptorPinNumbers”, 1 },
```

Свойство **UseDescriptorPinNumbers** указывает Windows, что нужно использовать естественную, а не последовательную нумерацию контактов. Если свойство UseDescriptorPinNumbers не задано или значение равно нулю, Windows по умолчанию будет использовать последовательную нумерацию контактов.

Если используется естественная нумерация контактов, необходимо также задать свойство **PinCount**.

```cpp
Package (2) { “GPIO-PinCount”, 54 },
```

Свойство **PinCount** должно соответствовать значению, возвращаемому свойству **TotalPins** в обратном вызове [CLIENT_QueryControllerBasicInformation](https://docs.microsoft.com/windows-hardware/drivers/ddi/content/gpioclx/nc-gpioclx-gpio_client_query_controller_basic_information) драйвера `GpioClx`.

Выберите схему нумерации, которая более всего соответствует существующей опубликованной документации для вашей платы. Так, Raspberry Pi использует естественную нумерацию, потому что на многих существующих диаграммах вывода используются номера контактов BCM2835. MinnowBoardMax использует последовательную нумерацию, потому что существующих диаграмм вывода не так много, а последовательная нумерация упрощает работу разработчиков, потому что лишь 10 контактов из 200 используются для вывода. Решение об использовании последовательной или естественной нумерации контактов должно избавить разработчиков от путаницы.

#### <a name="gpio-driver-requirements"></a>Требования драйвера GPIO

- Необходимо использовать`GpioClx`
- Сопоставление памяти on-SOC
- Использование эмулированной обработки прерываний ActiveBoth

### <a name="uart"></a>UART

Если драйвер UART использует `SerCx` или `SerCx2` , можно использовать рхпрокси для предоставления драйвера в пользовательском режиме. Драйверам UART, которые создают интерфейс устройства типа `GUID_DEVINTERFACE_COMPORT`, не требуется использовать rhproxy. Один из таких случаев — встроенный драйвер `Serial.sys`.

Чтобы предоставить `SerCx` стиль UART в пользовательском режиме, объявите `UARTSerialBus` ресурс следующим образом.

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

Только поле ResourceSource является фиксированным, в то время как все остальные поля — это заполнители для значений, которые во время выполнения задает пользователь.

Соответствующее объявление понятного имени имеет вид:+

```cpp
Package(2) { "bus-UART-UART2", Package() { 2 }},
```

Это Присваивает контроллеру понятное имя "UART2", которое представляет собой идентификатор, который пользователи будут использовать для доступа к шине из пользовательского режима.

## <a name="runtime-pin-muxing"></a>Мультиплексирование контактов в среде выполнения

Мультиплексирование контактов — это возможность использовать один и тот же физический контакт в разных целях. Несколько других чиповых периферийных устройств, таких как контроллер I2C, контроллер SPI и контроллер GPIO, можно перенаправить на тот же физический контакт в SOC. Модуль мультиплексирования контролирует активность функций контакта в конкретный момент. Как правило, встроенное ПО отвечает за назначение функций при загрузке и назначение остается статическим на протяжении всего сеанса загрузки. Мультиплексирование контактов во время выполнения добавляет возможность перенастройки назначений функций контактов во время выполнения. Предоставление пользователям возможности выбрать функцию контакта во время выполнения ускоряет разработку, потому что пользователи могут быстро перенастроить контакты платы, и обеспечивает аппаратную поддержку более широкого диапазона сфер применения по сравнению со статической конфигурацией.

Пользователи могут пользоваться поддержкой мультиплексирования для GPIO, I2C, SPI и UART, не создавая дополнительный код. Когда пользователь открывает GPIO или шину с использованием [OpenPin()](https://docs.microsoft.com/uwp/api/windows.devices.gpio.gpiocontroller.openpin) или [FromIdAsync()](https://docs.microsoft.com/uwp/api/windows.devices.i2c.i2cdevice.fromidasync), соответствующие физические контакты автоматически мультиплексируются для запрошенной функции. Если контакты уже используются другой функцией, вызов OpenPin() или FromIdAsync() завершится ошибкой. Когда пользователь закрывает устройство, удаляя объект [GpioPin](https://docs.microsoft.com/uwp/api/windows.devices.gpio.gpiopin), [I2cDevice](https://docs.microsoft.com/uwp/api/windows.devices.i2c.i2cdevice), [SpiDevice](https://docs.microsoft.com/uwp/api/windows.devices.spi.spidevice) или [SerialDevice](https://docs.microsoft.com/uwp/api/windows.devices.serialcommunication.serialdevice), контакты снова становятся свободными, что позволяет в дальнейшем использовать их для других функций.

В Windows реализована встроенная поддержка мультиплексирования контактов на платформах [GpioClx](https://docs.microsoft.com/windows-hardware/drivers/ddi/content/index), [SpbCx](https://docs.microsoft.com/windows-hardware/drivers/spb/spb-framework-extension) и [SerCx](https://docs.microsoft.com/windows-hardware/drivers/ddi/content/index). Эти платформы взаимодействуют, чтобы автоматически переключать контакт на нужную функцию, когда осуществляется доступ к контакту или шине GPIO. Доступ к контактам осуществляется произвольно с целью предотвращения конфликтов между несколькими клиентами. В дополнение к этой встроенной поддержке интерфейсы и протоколы для мультиплексирования контактов являются универсальными и могут быть расширены для обеспечения поддержки дополнительных устройств и сценариев.

В этом документе сначала описываются базовые интерфейсы и протоколы, участвующие в мультиплексировании контактов, а затем описывается добавление поддержки мультиплексирования контактов в драйверах контроллера GpioClx, SpbCx и SerCx.

### <a name="pin-muxing-architecture"></a>Архитектура мультиплексирования контактов

В этом разделе описываются основные интерфейсы и протоколы, участвующие в мультиплексировании контактов. Знание основных протоколов не обязательно для поддержки мультиплексировании контактов с драйверами GpioClx/SpbCx/SerCx. Дополнительные сведения о поддержке мультиплексирования контактов с драйверами GpioCls/SpbCx/SerCx см. в разделах [Реализация поддержки мультиплексирования контактов в драйверах клиента GpioClx](#supporting-muxing-support-in-gpioclx-client-drivers) и [Реализация поддержки мультиплексирования в драйверах контроллера SpbCx и SerCx](#supporting-muxing-in-spbcx-and-sercx-controller-drivers).

Мультиплексирование контактов осуществляется взаимодействием нескольких компонентов.

- Серверы мультиплексирования контактов — это драйверы, контролирующие блок управления мультиплексирования контактов. Закреплять серверы мультиплексированием получают пин-коды мультиплексированием запросов от клиентов посредством запросов на резервирование ресурсов мультиплексированием (через запросы *IRP_MJ_CREATE*) и запросы на переключение функции ПИН-кода (via * IOCTL_GPIO_COMMIT_FUNCTION_CONFIG_PINS-запросы). Сервер мультиплексирования контактов — это, как правило, драйвер GPIO, поскольку блок мультиплексирования иногда является частью блока GPIO. Даже если блок мультиплексирования — отдельное периферийное устройство, драйвер GPIO является наиболее логичным расположением для размещения функциональности мультиплексирования.
- Клиенты мультиплексирования контактов — драйверы, использующие мультиплексирование контактов. Клиенты мультиплексирования контактов получают ресурсы от промежуточного ПО ACPI. Ресурсы мультиплексирования контактов — это тип ресурса подключения, управляемый центром ресурсов. Клиенты мультиплексирования контактов резервируют ресурсы, открывая дескриптор для ресурса. Для внесения аппаратных изменений необходимо зафиксировать конфигурацию, отправив запрос *IOCTL_GPIO_COMMIT_FUNCTION_CONFIG_PINS*. Клиенты освобождают ресурсы мультиплексирования контактов, закрывая дескриптор. На этом этапе конфигурация мультиплексирования возвращается в состояние по умолчанию.
- Встроенное ПО ACPI — определяет конфигурацию мультиплексирования с ресурсами `MsftFunctionConfig()`. Ресурсы MsftFunctionConfig показывают, какие контакты и в каких конфигурациях мультиплексирования необходимы клиенту. Ресурсы MsftFunctionConfig содержат номер функции, конфигурацию запроса и список номеров контактов. Ресурсы MsftFunctionConfig предоставляются клиентам мультиплексирования контактов в качестве аппаратных; их получают драйверы в ходе обратного вызова PrepareHardware аналогично ресурсам подключения GPIO и SPB. Клиенты получают ИД центра ресурсов, который можно использовать для открытия дескриптора ресурса.

> Необходимо передать `asl.exe` параметр командной строки `/MsftInternal` , чтобы собрать ASL-файлы, содержащие дескрипторы `MsftFunctionConfig()`, поскольку эти дескрипторы в данный момент рассматриваются рабочим комитетом ACPI. Пример: `asl.exe /MsftInternal dsdt.asl`

Ниже показана последовательность операций мультиплексирования контактов.

![Взаимодействие клиент-сервер в процессе мультиплексирования контактов](images/usermode-access-diagram-1.png)

1. Клиент получает ресурсы MsftFunctionConfig от встроенного ПО ACPI в ходе обратного вызова [EvtDevicePrepareHardware()](https://docs.microsoft.com/windows-hardware/drivers/ddi/content/wdfdevice/nc-wdfdevice-evt_wdf_device_prepare_hardware).
2. Клиент использует вспомогательную функцию центра ресурсов `RESOURCE_HUB_CREATE_PATH_FROM_ID()` для создания пути из кода ресурса, который открывает дескриптор для пути (с помощью [ZwCreateFile()](https://docs.microsoft.com/windows-hardware/drivers/ddi/content/ntifs/nf-ntifs-ntcreatefile), [IoGetDeviceObjectPointer()](https://docs.microsoft.com/windows-hardware/drivers/ddi/content/wdm/nf-wdm-iogetdeviceobjectpointer) или [WdfIoTargetOpen()](https://docs.microsoft.com/windows-hardware/drivers/ddi/content/wdfiotarget/nf-wdfiotarget-wdfiotargetopen)).
3. Сервер извлекает ИД центра ресурсов из пути файла, используя вспомогательные функции центра ресурсов `RESOURCE_HUB_ID_FROM_FILE_NAME()`, а затем запрашивает дескриптор ресурса в центре ресурсов.
4. Сервер выполняет арбитраж общего доступа для каждого контакта в дескрипторе и выполняет запрос IRP_MJ_CREATE.
5. Клиент выпускает запрос *IOCTL_GPIO_COMMIT_FUNCTION_CONFIG_PINS* на полученном дескрипторе.
6. В ответ на запрос *IOCTL_GPIO_COMMIT_FUNCTION_CONFIG_PINS* сервер выполняет аппаратную операцию мультиплексирования, активируя указанную функцию на всех контактах.
7. Клиент продолжает операции, которые зависят от конфигурации запрошенного мультиплексирования контактов.
8. Когда клиент более не требует мультиплексирования контактов, он закрывает дескриптор.
9. В ответ на это сервер возвращает контакты в исходное состояние.

### <a name="protocol-description-for-pin-muxing-clients"></a>Описание протокола для клиентов мультиплексирования контактов

В этом разделе описано использование функции мультиплексирования контактов клиентом Это не относится к драйвера контроллера `SerCx` и `SpbCx`, поскольку платформа реализует этот протокол от имени драйверов контроллера.

#### <a name="parsing-resources"></a>Анализ ресурсов

Драйвер WDF получает ресурсы `MsftFunctionConfig()` в рамках процедуры [EvtDevicePrepareHardware()](https://docs.microsoft.com/windows-hardware/drivers/ddi/content/wdfdevice/nc-wdfdevice-evt_wdf_device_prepare_hardware). Ресурсы MsftFunctionConfig можно идентифицировать по следующим полям.

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

#### <a name="reserving-and-committing-resources"></a>Резервирование и фиксация ресурсов

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

Драйвер сохраняет WDFIOTARGET в одной из контекстных областей, чтобы впоследствии закрыть его. Когда драйвер готов освободить конфигурацию мультиплексирования, он закрывает дескриптор ресурса, вызывая метод [WdfObjectDelete()](https://docs.microsoft.com/windows-hardware/drivers/ddi/content/wdfobject/nf-wdfobject-wdfobjectdelete) или [WdfIoTargetClose()](https://docs.microsoft.com/windows-hardware/drivers/ddi/content/wdfiotarget/nf-wdfiotarget-wdfiotargetclose), если планируется использовать WDFIOTARGET повторно.

```cpp
    WdfObjectDelete(resourceHandle);
```

Когда клиент закрывает дескриптор ресурсы, контакты возвращаются в исходное состояние. Теперь они доступны и для других клиентов.

### <a name="protocol-description-for-pin-muxing-servers"></a>Описание протокол для серверов мультиплексирования контактов

В этом разделе описывается, как сервер мультиплексирования контактов предоставляет свою функциональность клиентам. Это не относится к драйверам мини-портов `GpioClx`, поскольку платформа реализует этот протокол от имени драйверов клиента. Подробные сведения о поддержке мультиплексирования контактов в клиентских драйверах `GpioClx` см. в разделе [Реализация поддержки мультиплексирования в клиентских драйверах GpioClx](#supporting-muxing-support-in-gpioclx-client-drivers).

#### <a name="handling-irp_mj_create-requests"></a>Обработка запросов IRP_MJ_CREATE

Клиенты открывают дескриптор для ресурса, когда требуется зарезервировать ресурс мультиплексирования контактов. Сервер мультиплексирования контактов получает запросы *IRP_MJ_CREATE* путем выполнения повторного анализа центра ресурсов. Конечный компонент пути запроса *IRP_MJ_CREATE* содержит код центра ресурсов, который является 64-битовым целым числом в шестнадцатеричном формате. Сервер должен извлечь ИД центра ресурсов из имени файла с помощью `RESOURCE_HUB_ID_FROM_FILE_NAME()` в reshub.h и отправить *IOCTL_RH_QUERY_CONNECTION_PROPERTIES* в центр ресурсов для получения дескриптора `MsftFunctionConfig()`.

Сервер должен проверить дескриптор и извлечь из него режим общего доступа и список контактов. Затем он должен выполнить арбитраж общего доступа контактов и в случае успешного завершения — пометить контакты как зарезервированные перед выполнением запроса.

Арбитраж общего доступа завершается успешно в целом, если он завершается успешно для всех контактов в списке. Арбитраж каждого контакта выполняется следующим образом.

- Если контакт еще не зарезервирован, арбитраж общего доступа завершается успешно.
- Если контакт уже зарезервирован как монопольный, происходит сбой арбитража.
- Если контакт уже зарезервирован как общий,
  - и входящий запрос используется совместно, арбитраж выполняется успешно.
  - а входящий запрос является монопольным, происходит сбой арбитража.

Если происходит сбой арбитража общего доступа, запрос необходимо обработать с помощью *STATUS_GPIO_INCOMPATIBLE_CONNECT_MODE*. Если арбитраж общего доступа выполняется успешно, запрос обрабатывается с помощью *STATUS_SUCCESS*.

Обратите внимание, что режим общего доступа входящих запросов должен быть получен из дескриптора MsftFunctionConfig, а не из [IrpSp->Parameters.Create.ShareAccess](https://docs.microsoft.com/windows-hardware/drivers/ifs/irp-mj-create).

#### <a name="handling-ioctl_gpio_commit_function_config_pins-requests"></a>Обработка запросов IOCTL_GPIO_COMMIT_FUNCTION_CONFIG_PINS

После того как клиент успешно резервирует ресурс MsftFunctionConfig, открывая дескриптор, можно отправить *IOCTL_GPIO_COMMIT_FUNCTION_CONFIG_PINS*, чтобы отправить на сервер запрос о выполнении фактической операции аппаратного мультиплексирования. Когда сервер получает запрос *IOCTL_GPIO_COMMIT_FUNCTION_CONFIG_PINS*, для каждого контакта в списке необходимо выполнить следующие действия:

- Задайте аппаратный режим запроса, заданный в члене PinConfiguration member структуры PNP_FUNCTION_CONFIG_DESCRIPTOR.
- Выполните мультиплексирование контакта для функции, заданной членом FunctionNumber структуры PNP_FUNCTION_CONFIG_DESCRIPTOR.

Затем сервер обязан выполнить запрос с помощью *STATUS_SUCCESS*.

Значение FunctionNumber определяется сервером; исходят из того, что дескриптор MsftFunctionConfig был создан специалистом, который знает, как сервер интерпретирует это поле.

Помните, что когда дескриптор закрывается, серверу необходимо вернуть контакты в состояние конфигурации,в котором они находились, когда был получен запрос IOCTL_GPIO_COMMIT_FUNCTION_CONFIG_PINS, поэтому серверу, возможно, потребуется сохранить их состояние до начала изменений.

#### <a name="handling-irp_mj_close-requests"></a>Обработка запросов IRP_MJ_CLOSE

Если клиенту более не требуется ресурс мультиплексирования, он закрывает дескриптор. Когда сервер получает запрос *IRP_MJ_CLOSE*, он должен вернуть контакты в состояние, в котором они находились на момент получения *IOCTL_GPIO_COMMIT_FUNCTION_CONFIG_PINS*. Если клиент не отправлял запрос *IOCTL_GPIO_COMMIT_FUNCTION_CONFIG_PINS*, никакие действия не требуются. Затем сервер должен пометить контакты как доступные для арбитража общего доступа и завершить запрос с помощью *STATUS_SUCCESS*. Обязательно синхронизируйте обработку запросов *IRP_MJ_CLOSE* и *IRP_MJ_CREATE*.

### <a name="authoring-guidelines-for-acpi-tables"></a>Инструкции по созданию таблиц ACPI

В этом разделе описывается, как предоставить ресурсы мультиплексирования клиентским драйверам. Обратите внимание, что для компиляции таблиц, содержащих ресурсы `MsftFunctionConfig()`, потребуется Microsoft ASL Compiler, сборка 14327 и выше. `MsftFunctionConfig()`предоставляются ресурсы для закрепления клиентов мультиплексированием в качестве аппаратных ресурсов. `MsftFunctionConfig()`ресурсы должны предоставляться драйверам, требующим изменения ПИН-кода мультиплексированием, которые обычно SPB и драйверы последовательного контроллера, но не должны передаваться в SPB и последовательные драйверы периферийных устройств, так как драйвер контроллера обрабатывает конфигурацию мультиплексированием.
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

- Общий/монопольный: если установлено значение "Монопольный", контакт может использоваться только одним клиентом в определенный момент. Если выбрано значение "Общий", использовать ресурс могут несколько клиентов. Всегда устанавливайте значение "Монопольный", поскольку в противном случае несколько нескоординированных клиентов получат доступ к изменяемому ресурсу, что может привести к конфликту данных и, следовательно, непредвиденным результатам.
- PinPullConfig — один из
  - PullDefault — используйте определенную SOC конфигурацию запроса по умолчанию на момент включения
  - PullUp — включите повышающий резистор
  - PullDown — включите понижающий резистор
  - PullNone — отключить все резисторы
- FunctionNumber — номер функции для программного использования в мультиплексировании.
- ResourceSource — путь пространства имен ACPI сервера мультиплексирования контакта
- ResourceSourceIndex — задайте значение 0.
- ResourceConsumer/ResourceProducer — задайте значение ResourceConsumer
- VendorData — необязательные двоичные данные. значение которых определяется сервером мультиплексирования контактов. Как правило, нужно оставить пустым.
- Список контактов — разделенный запятыми список номеров контактов, в отношении которых действует конфигурация. Если сервер мультиплексирования контактов является драйвером GpioClx, это номера контактов GPIO, они имеют тоже значение, что и номера контактов в дескрипторе GpioIo.

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

В дополнение к ресурсам памяти и прерывания, которые обычно требуются драйверу контроллера, также необходимо задать ресурс `MsftFunctionConfig()`. Этот ресурс позволяет драйверу контроллера I2C размещать в _SB контакты 2 и 3, управляемые узлом устройства \\ . GPIO0 — в функции 4 с включенным резистором по запросу.

## <a name="supporting-muxing-support-in-gpioclx-client-drivers"></a>Поддержка мультиплексирования в драйверах клиента GpioClx

`GpioClx`имеет встроенную поддержку для ПИН-кода мультиплексированием. Драйверы мини-портов GpioClx (драйверы клиента GpioClx), оборудование контроллера GPIO. Начиная с Windows 10, сборка 14327, драйверы мини-портов GpioClx могут добавлять поддержку мультиплексирования контактов, реализуя два новых DDI:

- CLIENT_ConnectFunctionConfigPins — вызывается `GpioClx`, чтобы отдать драйверу мини-портов команду применить заданную конфигурацию мультиплексирования.
- CLIENT_DisconnectFunctionConfigPins — вызывается `GpioClx`, чтобы отдать драйверу мини-портов команду отменить заданную конфигурацию мультиплексирования.

См. описание этих процедур в разделе [Функции обратного вызова события GpioClx](https://docs.microsoft.com/previous-versions/hh439464(v=vs.85)).

Помимо двух этих новых DDI необходимо проверить существующие DDI на совместимость с мультиплексированием контактов.

- CLIENT_ConnectIoPins/CLIENT_ConnectInterrupt — CLIENT_ConnectIoPins вызывается GpioClx, чтобы отдать команду драйверу мини-портов настроить набор контактов для ввода или вывода GPIO. GPIO является взаимно исключающим с MsftFunctionConfig, что означает, что контакт никогда не будет подключен к GPIO и MsftFunctionConfig одновременно. Поскольку функция контакта по умолчанию не обязательна должна быть GPIO, при вызове ConnectIoPins контакт не обязательно мультиплексируется для GPIO. ConnectIoPins необходим для выполнения всех операций по подготовке контакта к вводу/выводу GPIO, включая операции мультиплексирования. *CLIENT_ConnectInterrupt* должно вести себя аналогично, поскольку прерывания могут быть восприняты системой как особый случай ввода GPIO.
- CLIENT_DisconnectIoPins/CLIENT_DisconnectInterrupt — эта процедура должна возвращать контакты в состояние, в котором они находились на момент вызова CLIENT_ConnectIoPins/CLIENT_ConnectInterrupt, если только не задан флаг PreserveConfiguration. Помимо возвращения направления контактов в состояние по умолчанию мини-порт также должен возвращать состояние мультиплексора каждого контакта в состояние, в котором тот находился на момент вызова процедуры _Connect.

Допустим, конфигурация мультиплексора контакта по умолчанию — UART, и контакт также можно использовать в качестве GPIO. Если CLIENT_ConnectIoPins вызывается для подключения контакта для GPIO, он должен установить многоканальную связь между контактом и GPIO, а в CLIENT_DisconnectIoPins — установить многоканальную связь между контактом и UART. Как правило, подпрограммы отключения должны отменять операции, выполняемые подпрограммыми подключения.

## <a name="supporting-muxing-in-spbcx-and-sercx-controller-drivers"></a>Поддержка мультиплексирования в дисках контроллеров SpbCx и SerCx

Начиная с Windows 10, сборка 14327, платформы `SpbCx` и `SerCx` содержат встроенную поддержку мультиплексирования контактов, что позволяет дискам контроллеров `SpbCx` и `SerCx` осуществлять мультиплексирование контактов для клиентов, не внося никаких изменений в код самих дисков контроллеров. Аналогично, любой периферийный драйвер SpbCx/SerCx, который подключается к драйверу контроллеров SpbCx/SerCx с поддержкой мультиплексирования, инициирует мультиплексирование контактов.

На следующей схеме показаны зависимости между каждым из этих компонентов. Как видно, мультиплексирование контактов обуславливает зависимость драйверов контроллера SerCx и SpbCx от драйвера GPIO, который обычно отвечает за мультиплексирование.

![Зависимость мультиплексирования контактов](images/usermode-access-diagram-2.png)

Во время инициализации устройства платформы `SpbCx` и `SerCx` выполняют синтаксический разбор всех ресурсов `MsftFunctionConfig()`, предоставленных устройству в качестве аппаратных. Затем SpbCx/SerCx по требованию приобретают и предоставляют ресурсы мультиплексирования контактов.

`SpbCx`применяет конфигурацию мультиплексированием ПИН-кода в своем обработчике *IRP_MJ_CREATE* , непосредственно перед вызовом обратного вызова [евтспбтаржетконнект ()](https://docs.microsoft.com/windows-hardware/drivers/ddi/content/spbcx/nc-spbcx-evt_spb_target_connect) клиентского драйвера. Если применить конфигурацию мультиплексирования не удается, обратный вызов `EvtSpbTargetConnect()` драйвера контроллера выполнен не будет. Таким образом драйвер контроллера SPB может предположить, что контакты мультиплексируются в функцию к моменту вызова `EvtSpbTargetConnect()`.

`SpbCx`отменяет настройку ПИН-кода мультиплексированием в обработчике *IRP_MJ_CLOSE* , сразу после вызова обратного вызова [евтспбтаржетдисконнект ()](https://docs.microsoft.com/windows-hardware/drivers/ddi/content/spbcx/nc-spbcx-evt_spb_target_disconnect) драйвера контроллера. В результате получается, что контакты мультиплексируются для функции SPB всякий раз, когда периферийный драйвер открывает дескриптор драйверу контроллера SPB и отменяют мультиплексирование, когда периферийный драйвер закрывает дескриптор.

`SerCx`ведет себя аналогично. `SerCx`Получает все `MsftFunctionConfig()` ресурсы в своем обработчике *IRP_MJ_CREATE* непосредственно перед вызовом обратного вызова [EvtSerCx2FileOpen ()](https://docs.microsoft.com/windows-hardware/drivers/ddi/content/sercx/nc-sercx-evt_sercx2_fileopen) драйвера контроллера и освобождает все ресурсы в своем обработчике IRP_MJ_CLOSE, сразу же после вызова обратного вызова [EvtSerCx2FileClose](https://docs.microsoft.com/windows-hardware/drivers/ddi/content/sercx/nc-sercx-evt_sercx2_fileclose) драйвера контроллера.

Условие динамического мультиплексирования контакта для драйверов контроллера `SerCx` и `SpbCx` заключается в том, что они должны нормально обрабатывать отмену мультиплексирования контактов из функции SPB/UART. Драйверы контроллера должны исходить из того, что мультиплексирование контактов не выполняется до вызова `EvtSpbTargetConnect()` или `EvtSerCx2FileOpen()`. Контакты не обязательно мультиплексируются для функции SPB/UART во время следующих обратных вызовов. Следующий список не является исчерпывающим: в нем представлены наиболее распространенные процедуры PNP, реализуемые драйверами контроллеров.

- DriverEntry
- EvtDriverDeviceAdd
- EvtDevicePrepareHardware/EvtDeviceReleaseHardware
- EvtDeviceD0Entry/EvtDeviceD0Exit

## <a name="verification"></a>Проверка

Когда будете готовы переходить к тестированию rhproxy, желательно придерживаться следующей пошаговой процедуры.

1. Убедитесь, что каждый из драйверов контроллеров `SpbCx`, `GpioClx` и `SerCx` корректно загружается и функционирует.
1. Убедитесь, что `rhproxy` присутствует в системе. В некоторых выпусках и сборках Windows этого драйвера нет.
1. Скомпилируйте и загрузите узел рхпрокси с помощью`ACPITABL.dat`
1. Убедитесь, что узел устройств `rhproxy` существует.
1. Убедитесь, что `rhproxy`загружается и запускается.
1. Убедитесь, что ожидаемые устройства доступны в пользовательском режиме.
1. Убедитесь, что у вас получается взаимодействовать с каждым устройством из командной строки.
1. Убедитесь, что у вас получается взаимодействовать с каждым устройством из приложения UWP.
1. Проведите тесты HLK.

### <a name="verify-controller-drivers"></a>Проверка драйверов контроллеров

Так как рхпрокси предоставляет доступ к другим устройствам в пользовательском режиме, он работает только в том случае, если эти устройства уже работают. Прежде всего нужно убедиться, что эти устройства — контроллеры I2C, SPI, GPIO, которые вы хотите предоставить — уже работают.

В командной строке выполните команду

```ps
devcon status *
```

Просмотрите выходные данные и убедитесь, что все интересующие вас устройства запущены. Если для устройства указан код проблемы, необходимо выяснить, почему устройство не загружается, и устранить проблему. Все устройства должны были быть включены при первоначальном портировании платформы. Устранение неполадок с драйверами контроллеров `SpbCx`, `GpioClx` или `SerCx` выходит за рамки данного документа.

### <a name="verify-that-rhproxy-is-present-on-the-system"></a>Проверка присутствия rhproxy в системе

Убедитесь, что служба `rhproxy` присутствует в системе.

```ps
reg query HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Services\rhproxy
```

Если этот раздел реестра отсутствует, rhproxy нет в системе. Rhproxy присутствует во всех сборках Windows IoT Базовая и Windows Корпоративная начиная со сборки 15063.

### <a name="compile-and-load-asl-with-acpitabldat"></a>Компиляция и загрузка ASL с использованием файла ACPITABL.dat

Итак, вы создали узел rhproxy в ASL; теперь пора скомпилировать и загрузить его. Можно скомпилировать узел rhproxy в виде отдельного файла AML, который можно добавить к системным таблицам ACPI. Другой вариант — если у вас есть доступ к источникам ACPI, вы можете вставить узел rhproxy непосредственно в таблицы ACPI своей платформы. Однако во время первоначального портирования, скорее всего, удобнее использовать `ACPITABL.dat`.

1. Создайте файл с именем yourboard.asl и поместите узел устройств RHPX в DefinitionBlock:

    ```cpp
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

2. Скачайте [WDK](https://docs.microsoft.com/windows-hardware/drivers/download-the-wdk) и найдите `asl.exe` по адресу`C:\Program Files (x86)\Windows Kits\10\Tools\x64\ACPIVerify`
3. Выполните следующую команду, чтобы создать ACPITABL.dat:

    ```ps
    asl.exe yourboard.asl
    ```

4. Скопируйте созданный файл ACPITABL.dat в папку c: \Windows\system32 в тестируемой системе.
5. Включите testsigning в тестируемой системе.

    ```ps
    bcdedit /set testsigning on
    ```

6. Перезагрузить тестируемую систему. Система добавляет таблицы ACPI, определенные в ACPITABL.dat, в таблицы встроенного ПО.

### <a name="verify-that-the-rhproxy-device-node-exists"></a>Проверка существования узла устройств rhproxy

Выполните следующую команду, чтобы перечислить узел устройств rhproxy.

```ps
devcon status *msft8000
```

В выходных данных devcon должно быть указано, что устройство присутствует. Если узел устройств не присутствует, таблицы ACPI не были успешно добавлены в систему.

### <a name="verify-that-rhproxy-is-loading-and-starting"></a>Проверка загрузки и запуска rhproxy

Проверьте состояние rhproxy:

```ps
devcon status *msft8000
```

Если в выходных данных указано, что rhproxy запущен, это значит, что rhproxy успешно загрузился и запустился. Если вы видите код проблемы, необходимо изучить ситуацию. Некоторые распространенные коды проблем:

- Проблема 51 — `CM_PROB_WAITING_ON_DEPENDENCY` —-система не запускает rhproxy из-за того, что не удалось загрузить одну из его зависимостей. Это означает, что либо ресурсы, переданные rhproxy, указывают на недействительные узлы ACPI, либо целевые устройства не запускаются. Прежде всего проверьте еще раз, что все устройства успешно запущены (см. раздел "Проверка драйверов контроллеров" выше). Затем проверьте правильность АСЛ и убедитесь, что все пути к ресурсам (например, `\_SB.I2C1` ) правильны, и укажите допустимые узлы в дсдт.
- Проблема 10 — `CM_PROB_FAILED_START` — не удалось запустить rhproxy, скорее всего из-за проблемы с синтаксическим анализом ресурса. Просмотрите ASL и еще раз проверьте индексы ресурсов в DSD, а также убедитесь, что ресурсы GPIO указаны в порядке возрастания номера контакта.

### <a name="verify-that-the-expected-devices-are-exposed-to-user-mode"></a>Убедитесь, что ожидаемые устройства доступны в пользовательском режиме.

Теперь, когда рхпрокси работает, он должен создать интерфейсы устройств, доступ к которым можно получить в пользовательском режиме. Мы используем несколько средств командной строки для перечисления устройств и проверим, присутствуют ли они.

Клонировать [https://github.com/ms-iot/samples](https://github.com/ms-iot/samples) репозиторий и собрать `GpioTestTool` примеры, `I2cTestTool` , `SpiTestTool` и `Mincomm` . Скопируйте средства на свое устройство в тестовую папку и используйте следующие команды для перечисления устройств.

```ps
I2cTestTool.exe -list
SpiTestTool.exe -list
GpioTestTool.exe -list
MinComm.exe -list
```

Вы должны увидеть список своих устройств с их понятными именами. Если вы не видите нужные устройства и понятные имена, проверьте еще раз ASL.

### <a name="verify-each-device-on-the-command-line"></a>Проверка каждого устройства из командной строки

Следующий шаг — это использование средств командной строки для открытия устройств и взаимодействия с ними.

Пример I2CTestTool:

```ps
I2cTestTool.exe 0x55 I2C1
> write {1 2 3}
> read 3
> writeread {1 2 3} 3
```

Пример SpiTestTool:

```ps
SpiTestTool.exe -n SPI1
> write {1 2 3}
> read 3
```

Пример GpioTestTool:

```ps
GpioTestTool.exe 12
> setdrivemode output
> write 0
> write 1
> setdrivemode input
> read
> interrupt on
> interrupt off
```

Пример MinComm (serial): Подключите Rx к Tx перед запуском:

```ps
MinComm "\\?\ACPI#FSCL0007#3#{86e0d1e0-8089-11d0-9ce4-08003e301f73}\0000000000000006"
(type characters and see them echoed back)
```

### <a name="verify-each-device-from-a-uwp-app"></a>Проверка каждого устройства из приложения UWP

Используйте следующие примеры, чтобы проверить работу устройств из UWP.

- [IoT-GPIO](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/IoT-GPIO)
- [IoT-I2C](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/IoT-I2C)
- [IoT-SPI](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/IoT-SPI)
- [CustomSerialDeviceAccess](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/CustomSerialDeviceAccess)

### <a name="run-the-hlk-tests"></a>Выполнение тестов HLK

Скачайте [комплект проектирования оборудования (HLK)](https://docs.microsoft.com/windows-hardware/test/hlk/windows-hardware-lab-kit). Доступны следующие тесты:

- [Функциональные и нагрузочные тесты GPIO WinRT](https://docs.microsoft.com/windows-hardware/test/hlk/testref/f1fc0922-1186-48bd-bfcd-c7385a2f6f96)
- [Тесты записи I2C WinRT (требуется EEPROM)](https://docs.microsoft.com/windows-hardware/test/hlk/testref/2ab0df1b-3369-4aaf-a4d5-d157cb7bf578)
- [Тесты чтения I2C WinRT (требуется EEPROM)](https://docs.microsoft.com/windows-hardware/test/hlk/testref/ca91c2d2-4615-4a1b-928e-587ab2b69b04)
- [Тесты на несуществующие подчиненные адреса I2C WinRT](https://docs.microsoft.com/windows-hardware/test/hlk/testref/2746ad72-fe5c-4412-8231-f7ed53d95e71)
- [Расширенные функциональные тесты I2C WinRT (требуется mbed LPC1768)](https://docs.microsoft.com/windows-hardware/test/hlk/testref/a60f5a94-12b2-4905-8416-e9774f539f1d)
- [Тесты для проверки тактовой частоты SPI WinRT (требуется mbed LPC1768)](https://docs.microsoft.com/windows-hardware/test/hlk/testref/50cf9ccc-bbd3-4514-979f-b0499cb18ed8)
- [Тесты передачи ввода-вывода SPI WinRT (требуется mbed LPC1768)](https://docs.microsoft.com/windows-hardware/test/hlk/testref/00c892e8-c226-4c71-9c2a-68349fed7113)
- [Тесты для проверки шага SPI WinRT](https://docs.microsoft.com/windows-hardware/test/hlk/testref/20c6b079-62f7-4067-953f-e252bd271938)
- [Тесты для выявления разрывов в передаче SPI WinRT (требуется mbed LPC1768)](https://docs.microsoft.com/windows-hardware/test/hlk/testref/6da79d04-940b-4c49-8f00-333bf0cfbb19)

При выборе узла устройств rhproxy в диспетчере HLK соответствующие тесты выбираются автоматически.

В диспетчере HLK выберите "Устройство прокси-сервера центра ресурсов":

![Экран диспетчера HLK](images/usermode-hlk-1.png)

Щелкните вкладку "Тесты" и выберите тесты I2C WinRT, Gpio WinRT и Spi WinRT.

![Экран диспетчера HLK](images/usermode-hlk-2.png)

Щелкните "Запустить выбранные". Дополнительная документация по каждому тесту доступна по правому щелчку мыши (необходимо выбрать команду "Описание теста" в контекстном меню).

## <a name="resources"></a>Ресурсы

- [Спецификация ACPI 5.0](http://acpi.info/spec.htm)
- [Asl.exe (Microsoft ASL Compiler)](https://msdn.microsoft.com/library/windows/hardware/dn551195.aspx)
- [Windows. Devices. GPIO](https://msdn.microsoft.com/library/windows/apps/windows.devices.gpio.aspx)
- [Windows. Devices. I2C](https://msdn.microsoft.com/library/windows/apps/windows.devices.i2c.aspx)
- [Windows.Devices.Spi](https://msdn.microsoft.com/library/windows/apps/windows.devices.spi.aspx)
- [Windows.Devices.SerialCommunication](https://msdn.microsoft.com/library/windows/apps/windows.devices.serialcommunication.aspx)
- [Платформа тестирования разработки и выполнения (TAEF)](https://msdn.microsoft.com/library/windows/hardware/hh439725.aspx)
- [SpbCx](https://msdn.microsoft.com/library/windows/hardware/hh450906.aspx)
- [GpioClx](https://msdn.microsoft.com/library/windows/hardware/hh439508.aspx)
- [SerCx](https://msdn.microsoft.com/library/windows/hardware/ff546939.aspx)
- [Тесты MITT I2C](https://msdn.microsoft.com/library/windows/hardware/dn919852.aspx)
- [GpioTestTool](https://github.com/microsoft/Windows-iotcore-samples/tree/6e473075bbe616e4d9ce90e67c6412fba661c337/BusTools/GpioTestTool)
- [I2cTestTool](https://github.com/microsoft/Windows-iotcore-samples/tree/6e473075bbe616e4d9ce90e67c6412fba661c337/BusTools/I2cTestTool)
- [SpiTestTool](https://github.com/microsoft/Windows-iotcore-samples/tree/6e473075bbe616e4d9ce90e67c6412fba661c337/BusTools/SpiTestTool)
- [MinComm (послед.)](https://github.com/microsoft/Windows-iotcore-samples/tree/6e473075bbe616e4d9ce90e67c6412fba661c337/BusTools/MinComm)
- [Комплект проектирования оборудования (HLK)](https://msdn.microsoft.com/library/windows/hardware/dn930814.aspx)

## <a name="appendix"></a>Приложение

### <a name="appendix-a---raspberry-pi-asl-listing"></a>Приложение А. Списки ASL в Raspberry Pi

См. также [Raspberry Pi 2 & 3 сопоставления ПИН-кодов](/windows/iot-core/learn-about-hardware/pinmappings/pinmappingsrpi)

```cpp
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

См. также [MinnowBoard максимальное число сопоставлений ПИН-кодов](/windows/iot-core/learn-about-hardware/pinmappings/pinmappingsmbm)

```cpp
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

```ps
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
