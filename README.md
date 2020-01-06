# Работа с curl 

Использование утилиты curl

## Протокол http в двух словах
Когда клиент подключается к example.com и запрашивает ресурс /, он передает [Метод](https://ru.wikipedia.org/wiki/HTTP#%D0%9C%D0%B5%D1%82%D0%BE%D0%B4%D1%8B), [URI](https://en.wikipedia.org/wiki/Uniform_Resource_Identifier), [версию http](https://ru.wikipedia.org/wiki/HTTP#%D0%98%D1%81%D1%82%D0%BE%D1%80%D0%B8%D1%8F_%D1%80%D0%B0%D0%B7%D0%B2%D0%B8%D1%82%D0%B8%D1%8F), а так же [заголовки](https://ru.wikipedia.org/wiki/HTTP#%D0%97%D0%B0%D0%B3%D0%BE%D0%BB%D0%BE%D0%B2%D0%BA%D0%B8), к примеру: 
```
GET / HTTP/1.1
User-agent: curl/2000
Host: example.com
```
Примерный ответ сервера + тело ответа "hello":
```
HTTP/1.1 200 OK
Server: example-server/1.1
Content-Length: 5
Content-Type: plain/text

hello
```
Первая цифра кода ответа является своего рода "классом ошибок"
* 1xx: информационный
* 2xx: успех 
* 3xx: перенаправление
* 4xx: клиент запросил что-то, что сервер не мог или не хотел доставить 
* 5xx: проблема на сервере

## Примеры использования:
Использования нескольких методов запроса в одном запросе (POST, HEAD)
```
curl --location http://example.com/1 --next
  --data sendthis http://example.com/2 --next
  --head http://example.com/3
```
Запрос изображений от 1 до 100, которые инкременты двум, алфавитный диапозон, список, комбинирование:
```
curl -O http://example.com/[0-100:2].png
curl -O http://example.com/section[a-z].html
curl -O http://example.com/{one,two,three,alpha,beta}.html
curl -O http://example.com/{web,mail}-log[0-6].txt
```
Сохранение странциы с двух разных сайтов:
```
curl http://{site,host}.host[1-5].example.com -o "subdir/#1_#2"
```
Иногда требуется передать большое количество значений, воспользуемся чтением из файла:
```
curl -K cmdline.txt http://example.com
```
Передача логина:пароля для аутентификации
```
 curl -u user:12345 http://example.com/
```
Cжатие
```
curl --tr-encoding http://example.com/
``` 
Ограничение скачивания
```
curl https://example.com/ --limit-rate 200K
```
Максимальный размер файла для загрузки 
```
curl --max-filesize 100000 https://example.com/
```
Назначение определенных портов 
```
curl --local-port 4000-4200 https://example.com/
```
Через определенный интерфейс или IP адрес
```
curl --interface eth1 https://www.example.com/
curl --interface 192.168.0.2 https://www.example.com/
curl --interface machine2 https://www.example.com/
```
Ограничение времени подключения:
```
curl --connect-timeout 2.781 https://example.com/
```
Перенаправление:
```
curl --connect-to www.example.com:80:load1.example.com:80 http://www.example.com
```
Отправка заголовка Host
```
curl -H "Host: www.example.com" http://localhost/
```
Если в течении 15 сек скорость меньше 1000 байт, дропнуть соединение.
```
curl --speed-time 15 --speed-limit 1000 https://example.com/
```
Использование Proxy
```
curl -x proxy.example.com:80 https://example.com/
curl -U daniel:secr3t -x myproxy:80 http://example.com
curl --proxy-header "User-Agent: magic/3000" -x proxy https://example.com/
```
Заполнение форм
```
curl -F person=anonymous -F secret=@file.txt http://example.com/submit.cgi
```
Замена заголовка 
```
curl -F 'name=Dan' -H 'Content-Type: multipart/magic' https://example.com
```
Сохраняем cookie, создание новых cookie
```
curl -c cookie-jar.txt https://yandex.ru -o /dev/null
curl -j -b cookies.txt http://example.com
curl -d user=daniel -d secret=qwerty -d id=bc76 https://example.com/login.cgi \ -b cookies -c cookies -o out
```
### Диагностика
Для вывода диагностики требуется добавить -v, если данной информации не достаточно, можно воспользоваться трассировкой:
* ```curl --trace dump http://example.com``` 
если вместо dump указать "-" это переправит вывод в stdout), так же можно поиграться со значениями --trace-acsii, --trace-time
### Фильтрация вывода
Выведем только Type, Code:
```curl -w "Type: %{content_type}\nCode: %{response_code}\n" http://example.com -o /dev/null```
### Help
* Список кодов состояния [Code_return](https://bagder.gitbook.io/everything-curl/usingcurl/usingcurl-returns)
* У curl  свыше 250 опций и с каждым годом значение все растет и растет. В основном для справок достаточно curl --help, curl - h, но проще воспользоваться веб версией: [MAN_web](https://curl.haxx.se/docs/manpage.html)
* curl --manual https://curl.haxx.se/docs/manpage.html |less
* Книга [curl](https://bagder.gitbook.io/everything-cu)
