## Проект домашнего задания для МТС Тета - Модуль нагрузочного тестирования
###  Требования/ Requirements
На НТ на АРМ/Сервере НТ должны быть установлены:
 - openjava11
 - jmeter
### Схема приложения [Ссылка на развернутое Приложение API из Домашного задания по модулю 2 (работает пока не выключено)](http://9f5f69f6-aa8c-4658-abf1-f6deeb4ebbba.mts-gslb.ru/WeatherForecast)
![схема приложения](https://github.com/coolf124-vlab101/mts-teta-hw01/blob/main/mts-teta-hw-01.drawio.png?raw=true)
### Список хостов
```
balancer-01 10.0.10.2 +publicIP
db-01 10.0.10.3
db-01 10.0.10.4
etcd-01 10.0.10.5
etcd-02 10.0.10.6
etcd-03 10.0.10.7
```
## 1. Разработать профиль нагрузки для системы
### Предположение 1 - Система будет использоваться для запроса прогнозов погоды, а вводных исходных будет заниматься несушественное время, которым можно принебречь
Профиль нагрузки:
 От каждого пользователя (thread)
 1 запросов типа curl -H "Host: 9f5f69f6-aa8c-4658-abf1-f6deeb4ebbba.mts-gslb.ru" http://91.185.85.213/Cities
 9 запросов типа curl -H "Host: 9f5f69f6-aa8c-4658-abf1-f6deeb4ebbba.mts-gslb.ru" http://91.185.85.213/WeatherForecast
 

## 2. Реализовать профиль на любом инструменте НТ (разработать скрипт)
### 2.1. Подготовка

```
sudo apt update
sudo apt upgrade -y
sudo apt install openjava11
sudo apt install xrdp
wget https://dlcdn.apache.org//jmeter/binaries/apache-jmeter-5.6.2.tgz
java -version
tar -xf apache-jmeter-5.6.2.tgz 
cd apache-jmeter-5.6.2
chmod +x jmeter.sh
cd lib/ext/
wget --content-disposition  https://jmeter-plugins.org/get/
./jmeter
#требуется через plugin manager установить plugin  - Custom Threads Groups
```
### 2.2  Запуск НТ 
```
./jmeter -n -t simple-http-request-test-plan2.jmx -l results-2023-12-04-01_13.log
```
## 3. Задать нефункциональные требования по производительности к системе (SLO/SLA)
SLI Latency 0.95 < 500 ms, Latency 0.99 < 1000 ms
SLI availability  0.99 / Error 4xx < 1%

## 4. Найти максимальную производительность системы
## 5. Написать краткий вывод: где достигнута максимальная производительность, где узкое место в системе, для подтверждения привести графики.
Максимальная производительность при 2-ух подах достигается на 900 запросах в секунду, тк максимальная производительность по RPS, при которой также выполняется SLA по Latency и ошибках
Узкое место - CPU limit на поды , тк в точках достижения максимальной производительности, все остальные ресурсы не нагружены до максимума. Если увеличит кол-во подов до 3, то  RPS max --> 1200-1300, что потверждает гипотезу.
![запуск1](https://github.com/coolf124-vlab101/mts-teta-monitoring/blob/main/test-run-01.png?raw=true)
![запуск2](https://github.com/coolf124-vlab101/mts-teta-monitoring/blob/main/test-run-02.png?raw=true)
![запуск3](https://github.com/coolf124-vlab101/mts-teta-monitoring/blob/main/test-run-03.png?raw=true)