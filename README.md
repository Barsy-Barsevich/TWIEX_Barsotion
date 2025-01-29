# TWIEX_Barsotion library 1.2

История обновлений:
- 1.2 (24.09.2023) - создан файл Keywords. изменена функция bool read(bool, uint8_t*)
- 1.1 (24.09.2023) - внесены правки, написаны примеры
- 1.0 (17.09.2023) - написана библиотека

Библиотека TWIEX_Barsotion.h - это лучшее решение для организации TWI мастера на микроконтроллере с архитектурой AVR, которое придумали люди.
Библиотека предоставляет возможность работать по шине TWI по прерываниям, что позволяет экономить машинное время ОЭВМ.
Библиотека позволяет оперативно находить сбои в работе шины TWI и их устранять.

Библиотека предлагает два набора функций: для общения по шине TWI по прерываниям и без.

В штатном режиме библиотека использует TIMER2 для подсчета времени. Однако если функции общения по прерываниям не используются, можно закомментировать строку 
```C
#define TWI_ON_INTERRUPTS
```
в файле TWIEX_Barsotion.h, и тогда TIMER2 использоваться не будет.

Библиотека рассчитана под работу с контроллерами на тактовой частоте 16МГц.

## Функции для общения без прерываний

- bool twi.writeRegister(uint8_t device_addr, uint8_t register, uint8_t data)
- bool twi.readRegister(uint8_t device_addr, uint8_t register, uint8_t* buf)
- bool twi._start(void)
- bool twi._write(uint8_t data)
- bool twi._read(bool ACKM, uint8_t* data)
- bool twi._stop(void)

Данные функции, включенные в библиотеку, позволяют записывать данные в любые регистры любых устройств шины TWI, а также считывать данные из любых регистров любых устройств шины TWI, используя при этом программные задержки. Применение этих функций целесообразно, если необходимо произвести единичный обмен данными между ОЭВМ и ведомым устройством, например, во время начальной настройки ведомого устройства. Все функции возвращают true или false в зависимости от того, удалась ли транзакция. При замыкании или обрыве линий TWI функции возвращают false, не вызывая при этом зависаний ОЭВМ.

## Функции работы с TWI по прерываниям

- void twi.startQueue(void)
- void twi.start(void)
- void twi.write(uint8_t data)
- void twi.request(bool Ack_Master)
- uint8_t twi.read(void)
- void twi.stop(void)
- void twi.setErrorTime( time_mks)

Данные функции созданы для общения по шине TWI по прерываниям. После отправки команды модулю TWI данные функции возвращают управление основному процессу ОЭВМ. В случае, если линии TWI не замыкаются на землю и между собой, а также не разорваны, через некоторое время приходит прерывание по вектору TWI\_vect, которое возвещает об окончании транзакции. Если прерывание TWI\_vect не пришло за время, установленное функцией setErrorTime, то вызывается прерывание TIMER2\_COMPA\_vect, подпрограмма обработки которого должна содержать обработчик ошибок. Примеры работы с шиной TWI по прерываниям можно видеть в папке examples.uint8_t
