# Модуль робота "Таракан"
-----------------------------

7 роботов данной модели использовались нами для демонстрации языка RCML на выставке в Robotics Expo II.
Каждый робот в своем составе имел:
- Arduino Mega
- Bluethooth передатчик HC-06
- Ультразвуковой дальномер HC-SR04
- Драйвер моторов L293D
- Два мотора постоянного вращения

Робот общался с ПК по Bluethooth, откуда получал команды на передвижение. 
Модуль имеет конфигурационный файл, в котором указывались адреса Bluethoth передатчика каждого робота, именно по ним определялось число доступных роботов. Выбор робота, из доступных, для выполнения очередной порции команд выполнялся случайным образом.
Так как использовались моторы постоянного вращения, то передвижение выполнялось с замером времени вращения моторов. Для этого использовались калибровочные таблицы, сопоставлявшие время и расстояние по линейному закону, данные таблицы задаются также через конфигурационный файл.

Данный модуль может служить примером для аналогичных роботов.
 
Модуль имеет конфигурационный файл, в котором указывается количество роботов, HEX адрес Bluetooth устройства а так же калибровочные таблицы для передвижения робота.<br>
Количество роботов указывается в разделе **main**, присваиванием параметру **count_robots** числа используемых роботов.<br>
Для каждого робота описывается раздел **tarakan_X**, где X - номер робота. В этом разделе параметру **connection** необходимо присвоить HEX адрес Bluetooth устройства (*Его можно посмотреть в свойствах Bluetooth устройства на ПК*).<br>
Калибровочные таблицы указываются для каждого робота в соответствующем разделе **tarakan_X**:<br>
 - Параметры для перемещения имеют вид **move_time_**[*Время в микросекундах*] = [*расстояние в сантиметрах*]<br>
 - Параметры для поворота имеют вид **rotate_time_**[*Время в микросекундах*] = [*градус поворота*]<br>

*Пример содержимого config.ini:*
```ini
	[main]
	count_robots = 1

	[tarakan_1]
	connection = 0X301408261889

	move_time_200 = 15
	move_time_500 = 30
	move_time_1000 = 60
	move_time_2000 = 120
	move_time_3000 = 240
	move_time_5000 = 360
	move_time_10000 = 720
	rotate_time_200 = 15
	rotate_time_500 = 30
	rotate_time_1000 = 60
	rotate_time_2000 = 120
	rotate_time_3000 = 240
	rotate_time_5000 = 360
	rotate_time_10000 = 720
```
*Стоит обратить внимание, что config.ini должен находиться в одной директории с файлом модуля.*

####Доступные функции робота:
Определение  | Описание
------------  | -----------------  
moveTo(*direction*, *distance*)  | Перемещение в зависимости от параметра *direction* (1 - вперед, 0 - назад) на указанное в параметре *distance* расстояние в сантиметрах. Функция значения не возвращает и бросает исключение, если робот встречает перед собой препятствие.
rotateTo(*direction*, *angle*)  | Вращение в зависимости от параметра *direction* (1 - направо, 0 - налево) на указанное в параметре *angle* количество градусов. Функция не возвращает значение и не бросает исключение.
moveToByTime(*direction*, *speed*, *time*)  | Перемещение в зависимости от параметра *direction* (1 - вперед, 0 - назад) в течении указанного в параметре *time* количества миллисекунд со скоростью *speed*(от 0 до 100 процентов от максимальной скорости). Функция значения не возвращает и бросает исключение, если робот встречает перед собой препятствие.
rotateToByTime(*direction*, *speed*, *time*)  | Вращение в зависимости от параметра *direction* (1 - направо, 0 - налево) в течении указанного в параметре *time* количества миллисекунд со скоростью *speed*(от 0 до 100 процентов от максимальной скорости). Функция не возвращает значение и не бросает исключение.
changeLightMode(*group*, *number*,*on*, *time*)  | Переключение работы группы световой индикации робота. Параметр *group* задает группу светодиодов: габаритные – 0, направляющие - 1. *number*: 1-4 - номер светодиода в группе, 0 - все светодиоды группы. *on*: 1 - включить, 0 - выключить. *time* - период мигания в миллисекундах.
getDistanceObstacle(*type*)  | Запрос расстояние до препятствия, принимает параметр *type* задает положение датчика: 1 - спереди или 0 - сзади.
stop() | Остановка перемещения робота


####Доступные оси робота
Наименование  | Верхняя граница  | Нижняя граница  | Примечание
------------  | -----------------  | ------------------  | ------------------
locked  | 1  | 0  | Бинарная ось. Если её значение равно 1, то робот считается заблокированным и игнорирует изменение значений по любым другим осям.
straight  | 200  | 0  | Дискретная ось. Постоянное прямолиненое движение: значение 2 - вперед, 1 - стоп, 0 - назад.
rotation  | 200  | 0  | Дискретная ось. Постоянное вращение покруг своей оси: значение 2 - направо, 1 - стоп, 0 - налево.

####Компиляция
Для компиляции модуля потребуется библиотека [SimpleIni](https://github.com/brofield/simpleini)