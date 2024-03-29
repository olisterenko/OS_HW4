### Листеренко Ольга Руслановна ###
### БПИ 217 ###
  
### Вариант 1 ###
### Задача ###
**Задача о парикмахере**. В тихом городке есть парикмахерская. Салон парикмахерской мал, работать в нем нем может только **один парикмахер**, обслуживающий **одного посетителя**. Есть несколько кресел для ожидания в очереди. Парикмахер всю жизнь обслуживает посетителей. Когда в салоне никого нет, он спит в кресле. Когда посетитель приходит и видит спящего парикмахера, он будит его, садится в кресло, «засыпая» на тот момент, пока парикмахер обслуживает его. Если посетитель приходит, а парикмахер занят, то он встает в очередь и «засыпает». После стрижки парикмахер сам провожает посетителя. Если есть ожидающие посетители, то парикмахер будит одного из них и ждет пока тот сядет в кресло парикмахера и начинает стрижку. Если никого нет, он снова садится в свое кресло и засыпает до прихода посетителя. **Создать приложение, моделирующее рабочий день парикмахерской**. *В
качестве отдельных клиентов выступают парикмахер и множество посетителей. То есть, все посетители - это отдельный клиент. Сервер выступает в роли очереди, осуществляющей синхронизацию работы парикмахера и поступающих посетителей*.  

### 4-5 баллов ###
Клиент–серверное приложение, в котором сервер (или серверы) и клиенты независимо друг от друга отображают только ту информацию, которая поступает им во время обмена.  

#### Сценарий ####
Первым запускается сервер, затем парикмахер и только потом клиенты. Для парикмахера и клиентов предназначены разные порты. При запуске сервера параметры выклядят так: "<IP> <Порт для клиентов> <Порт для парикмахера>". При запуске клиентов и парикмахера нужно указывать IP, а затем соответствующий порт. Если клиента запустить раньше парикмахера, то он будет ждать "у двери", пока парикмахер не придет, т.к. парикмахерская закрыта. Сервер сначала ожидает прихода парикмахера, а затем уже начинает обрабатывать клиентов. Когда клиент приходит, он посылает серверу свой pid (просто чтобы удобно было различать). Так клиент становится в очередь. Далее сервер пересылает pid клиента парикмахеру, тот стрижет 3 секунды, отправляет pid обратно, сервер принимает и пересылает клиенту, что означает, что клиент может уйти. Парикмахер изначально находится в состоянии ожидания (через recv()). По окончании работы каждой программы происходит закрытие сокетов. Выходить из программы можно по Ctrl+C.  
#### Результат ####  
<img width="736" alt="image" src="https://github.com/Milorann/OS_HW4/assets/57359954/72876275-2c0b-41f1-973a-2ee9ca2ce408">  
<img width="734" alt="image" src="https://github.com/Milorann/OS_HW4/assets/57359954/1455e1a6-862a-4fb4-b2b0-b8d03fb761e0">  
<img width="734" alt="image" src="https://github.com/Milorann/OS_HW4/assets/57359954/f80c13d4-5664-4fa0-b3af-c17df6f3d3ea">  
<img width="737" alt="image" src="https://github.com/Milorann/OS_HW4/assets/57359954/b0ce6615-5d23-4bb3-8852-1a86a3eccaab">  
  
### 6-7 баллов ###
Разработать клиентскую программу, подключаемую к серверу, которая предназначена для отображения комплексной информации о выполнении приложения в целом.  
  
Добавлена программа observer.c. После запуска сервера подключается observer, затем парикмахер и потом клиенты. При запуске наблюдателя нужно ввести IP и порт. Новые параметры для сервера: "<IP> <Порт для клиентов> <Порт для парикмахера> <Порт для наблюдателя>". client и hairdresser не менялись. В server добавлено "подключение" observer, а также отправка ему сообщений. Внутри самого observer запущен вечный цикл, в котором он выполняет только recv() (по протоколу UDP) и вывод полученной информации. При закрытии программы сокет также закрывается.  
#### Результат ####  
<img width="746" alt="image" src="https://github.com/Milorann/OS_HW4/assets/57359954/716e80c7-7d7e-4cfa-9e9b-cc582b828984">  

### 8 баллов ###
Возможность, подключения множества клиентов, обеспечивающих отображение информации о работе приложения.  
  
Поменялся только server. Теперь можно параллельно подключать наблюдателей (15 штук) и клиентов (неограничено). Принятие клиентов и наблюдателей происходит в параллельных тредах. При обработке клиента информация записывается в неименованный пайп. Как только пайп считывает информацию (это происходит в еще одном треде), он рассылает ее всем зарегестрированным наблюдателям. При отключении сервера закрываются сокеты, пайп, а также уничтожается мьютекс, нужный для корректной рассылки обзерверам. Тем не менее, парикмахера все еще нужно подключать первым после подключения сервера. Далее порядок подключения обезрверов и клиентов не важен.  
Можно было сделать широковещательную рассылку, но с ней возникают проблемы в рамках одного компьютера. Если делать ее, то не нужен пайп, дополнительная структура и мьютекс. Достаточно делать бинд в обзервере и отправлять информацию на сокет с сервера с помощью sendto().
  
<img width="861" alt="image" src="https://github.com/Milorann/OS_HW4/assets/57359954/61d18b2d-f35c-4fdf-9ac7-7294dc607b33">  

### 9 баллов ###
Возможность отключать и подключать различных клиентов с сохранением работоспособности сервера.
  
В программе, написанной на 8 можно отключать и подключать клиентов и обзерверов с любой момент.  
<img width="923" alt="image" src="https://github.com/Milorann/OS_HW4/assets/57359954/8aeecb4d-6286-4dee-ab9c-6b4e8eac1417">  
<img width="881" alt="image" src="https://github.com/Milorann/OS_HW4/assets/57359954/027b9cce-0015-4365-a1d2-d75b857e6847">  
  
