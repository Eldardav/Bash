1. Есть скрипт:

#!/usr/bin/env python3

a = 1

b = '2'

c = a + b

Какое значение будет присвоено переменной c?

Как получить для переменной c значение 12?

Как получить для переменной c значение 3?

Ответ:
1. будет ошибка,типы не соответсвуют для операции, int и str
2. a строка     c=str(a)+b
3. b к целому числу c=a+int(b)

2. Мы устроились на работу в компанию, где раньше уже был DevOps Engineer. Он написал скрипт, позволяющий узнать, какие файлы модифицированы в репозитории, относительно локальных изменений. Этим скриптом недовольно начальство, потому что в его выводе есть не все изменённые файлы, а также непонятен полный путь к директории, где они находятся. Как можно доработать скрипт ниже, чтобы он исполнял требования вашего руководителя?
>       #!/usr/bin/env python3
>       import os
>       bash_command = ["cd ~/netology/sysadm-homeworks", "git status"]
>       result_os = os.popen(' && '.join(bash_command)).read()
>       is_change = False
>       for result in result_os.split('\n'):
>          if result.find('modified') != -1:
>              prepare_result = result.replace('\tmodified:   ', '')
>              print(prepare_result)
>              break

Ответ:
Лишняя логическая переменная is_change и команда break которая прерывает обработку при первом же найденом вхождении

3. Доработать скрипт выше так, чтобы он мог проверять не только локальный репозиторий в текущей директории, а также умел воспринимать путь к репозиторию, который мы передаём как входной параметр. Мы точно знаем, что начальство коварное и будет проверять работу этого скрипта в директориях, которые не являются локальными репозиториями.

Ответ:
>       #!/usr/bin/env python3
>       import os
>       import sys
>       md = os.getcwd()
>       if len(sys.argv)>=2:
>          cmd = sys.argv[1]
>       bash_command = ["cd "+cmd, "git status 2>&1"]
>       print('\033[31m')
>       result_os = os.popen(' && '.join(bash_command)).read()
>       #is_change = False
>       for result in result_os.split('\n'):
>          if result.find('fatal') != -1:
>              print('\033[31m Каталог \033[1m '+cmd+'\033[0m\033[31m не является GIT репозиторием\033[0m')   
>          if result.find('изменено') != -1:
>             prepare_result = result.replace('\tизменено: ', '')
>       # добавил замену всех оставшихся пробелов в строке для удобства вывода
>              prepare_result = prepare_result.replace(' ', '')
>              print(cmd+prepare_result)
>       #        break


Вывод:
>       userqq@urt(0):~/python$ ./dz2.py ~/devops-netology/
>       /home/userqq/devops-netology/README.md
>       /home/userqq/devops-netology/dz5/dz.txt
>       /home/userqq/devops-netology/file4taguse
>       /home/userqq/devops-netology/renamed_file.txt
>       userqq@urt(0):~/python$ ./dz2.py ~/devops-net/
>       /bin/sh: 1: cd: can't cd to /home/userqq/devops-net/
>       userqq@urt(0):~/python$ ./dz2.py
>       Каталог  /home/userqq/python не является GIT репозиторием
>       userqq@urt(0):~/python$

4. Наша команда разрабатывает несколько веб-сервисов, доступных по http. Мы точно знаем, что на их стенде нет никакой балансировки, кластеризации, за DNS прячется конкретный IP сервера, где установлен сервис. Проблема в том, что отдел, занимающийся нашей инфраструктурой очень часто меняет нам сервера, поэтому IP меняются примерно раз в неделю, при этом сервисы сохраняют за собой DNS имена. Это бы совсем никого не беспокоило, если бы несколько раз сервера не уезжали в такой сегмент сети нашей компании, который недоступен для разработчиков. Мы хотим написать скрипт, который опрашивает веб-сервисы, получает их IP, выводит информацию в стандартный вывод в виде: <URL сервиса> - <его IP>. Также, должна быть реализована возможность проверки текущего IP сервиса c его IP из предыдущей проверки. Если проверка будет провалена - оповестить об этом в стандартный вывод сообщением: [ERROR] <URL сервиса> IP mismatch: <старый IP> <Новый IP>. Будем считать, что наша разработка реализовала сервисы: drive.google.com, mail.google.com, google.com.

Ответ:
>       ##!/usr/bin/env python3
>       import socket as s
>       import time as t
>       import datetime as dt
>       # set variables
>       i = 1
>       wait = 2 # интервал проверок в секундах
>       srv = {'drive.google.com':'0.0.0.0', 'mail.google.com':'0.0.0.0', 'google.com':'0.0.0.0'}
>       init=0
>       print('*** start script ***')
>       print(srv)
>       print('********************')
>       while 1==1 : #отладочное число проверок
>        for host in srv:
>         ip = s.gethostbyname(host)
>         if ip != srv[host]:
>           if i==1 and init !=1:
>              print(str(dt.datetime.now().strftime("%Y-%m-%d %H:%M:%S")) +' [ERROR] ' + str(host) +' IP mistmatch: '+srv[host]+' '+ip)
>            srv[host]=ip
>       # счетчик итераций для отладки, закомментировать для бесконечного цикла 3 строки
>        i+=1
>        if i >= 50 :
>          break
>        t.sleep(wait)
>       результат работы:
>       userqq(0):~/python$ ./dz3.py
>       >       *** start script ***
>       {'drive.google.com': '0.0.0.0', 'mail.google.com': '0.0.0.0', 'google.com': '0.0.0.0'}
>       ********************
>       2021-10-17 12:39:20 [ERROR] drive.google.com IP mistmatch: 0.0.0.0 173.194.73.194
>       2021-10-17 12:39:20 [ERROR] mail.google.com IP mistmatch: 0.0.0.0 64.233.161.83
>       2021-10-17 12:39:20 [ERROR] google.com IP mistmatch: 0.0.0.0 173.194.221.139
>       2021-10-17 12:40:46 [ERROR] mail.google.com IP mistmatch: 64.233.161.83 64.233.165.19
>       2021-10-17 12:40:48 [ERROR] mail.google.com IP mistmatch: 64.233.165.19 64.233.161.83
>       2021-10-17 12:40:52 [ERROR] google.com IP mistmatch: 173.194.221.139 64.233.165.101
>       2021-10-17 12:40:54 [ERROR] google.com IP mistmatch: 64.233.165.101 173.194.222.100
