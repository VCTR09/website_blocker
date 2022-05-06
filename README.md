<a id="anchor"></a>
# Программа-блокировщик сайтов

![IMG_1862](https://user-images.githubusercontent.com/97599612/167077377-4817808e-b148-4b0f-a7f5-0e2105b09738.JPG)


* Программа принимает сслыки на сайты, которые отвлекают от работы (напрмимер, YouTube, соцсети), а также рабочие часы. Соответственно, в эти часы, указанные веб-сайты становятся недоступны для просмотра.

* Программа полезна в снижении стресса, так как сайты, отвлекающие от работы, вызывают стресс.

* Программ легкая и работает в фоновом режиме, не влияя на производительность устройства.


### Принцип действия: 
Программа работает с файлом __hosts__, помещая в него адреса сайтов, которые надо заблокировать,
следом за локальным _IP_ адресом устройства. При попытке зайти на сайт, мы будем перенаправлены на _IP_ адрес, указанный в файле __hosts__. На экран будет выведена ошибка: __This site can't be reached / Не удается получить доступ к сайту__.

Соответсвенно, все что делает _Python_ скрипт это помещает адреса сайтов в файл __hosts__ в часы, указанные как рабочие, и удаляет эти адреса из файла в остальные часы.

### Начало создания

* Указываем путь до файла
> hosts_path = "/private/etc/hosts"

\# Так выглядит путь до файла __hosts__ на _MacOS_. На _Windows_ это может выглядеть как: _"C:\Windows\System32\drivers\etc\hosts"_

* Также 2 другие переменные:
> redirect = "127.0.0.1"  

\# IP адрес куда будет перенаправлен браузер.

> website_list = ["www.facebook.com", "facebook.com", "www.toyota.ru", "toyota.ru"]  

\# Cписок из сайтов, которые мы хотим заблокировать.


### Полный код программы с комментариями

```
import time  # модуль нужен лишь для того, чтобы запускать скрипт раз в указанный временной интервал
from datetime import datetime as dt  # модуль нужен для операций с датами, временем, получения разницы во времени, итд.

hosts_temp = "hosts"  # временный hosts файл, на котором можно отработать скрипт
hosts_path = "/private/etc/hosts"
redirect = "127.0.0.1"
website_list = ["www.facebook.com", "facebook.com", "www.toyota.ru", "toyota.ru"]

while True:
    if dt(dt.now().year, dt.now().month, dt.now().day, 8) < dt.now() < 
        dt(dt.now().year, dt.now().month, dt.now().day, 16):    # строчка dt(dt.now().year, dt.now().month, dt.now().day, 16) означает текущие год/месяц/день/16 часов. Все что нам надо указать это время. В данном случае 16. По сути все данное выражение можно заменить на if (8 < dt.now().hour < 16):
        print("Working hours...")  # если условие выше выполнено, выведим запись "Working hours..."
        with open(hosts_path, 'r+') as file:  # метод r+ позволяет читать из файла и записывать в файл
            content = file.read() # метод read
            # print(content)
            for website in website_list:
                if website in content:
                    pass  # если сайт уже записан в файле hosts, ничего не делать
                else:
                    file.write(redirect + " " + website + "\n")
    else:
        with open(hosts_path, 'r+') as file:  # открываем файл в режиме r+, используя контекстный менеджер with. Указатель (pointer) находится в начале файла.
            content = file.readlines()  # прочитать все строки. Теперь указатель находится в конце файла.
            file.seek(0)  # метод seek возвращает указатель в начало файла
            for line in content:
                if not any(website in line for website in website_list):
                    file.write(line)  # напишем все строки, которые мы хотим сохранить. Указатель находится сразу после последней написанной строки.
            file.truncate()  # удаляет все строки с текущей позиции до конца файла. Указатель находится в НОВОМ конце файла.
        print("Fun hours...")  # если мы не во временном промежутке между 8 и 16 часами, будет выведена запись "Fun hours..." 
    time.sleep(5)  # цикл выполняется каждые 5 секунд
```

** метод _read_ записывает содержимое файла в переменную в качестве единой строки.
** метод _readlines_ записывает содержимое файла в переменную в качестве списка всех строк по отдельности.

___
```
if not any(website in line for website in website_list):
    file.write(line)
```

Допустим, первая строка _(line)_ в файле _hosts_ это _'localhost is used to configure the loopback interface'_.
Если ни одного из элементов списка _"www.facebook.com", "facebook.com", "www.toyota.ru", "toyota.ru"_ нет в строке _'localhost is used to configure the loopback interface'_, то мы запишем строку 
_'localhost is used to configure the loopback interface' в файл. И так с каждой строкой.
___

### Запуск программы на MacOS:

> sudo nano /private/etc/hosts

\# Проверим если в файле _hosts_ находятся сайты, которые надо заблокировать.

> sudo python3 /Users/victorvoronezhskiy/Desktop/python_course/python_mega_projects/webiste_blocker/website_blocker.py   

\# Запуск скрипта.

> sudo crontab -e

\# Используется _crontab_ сервис.

[Ввeрх](#anchor)