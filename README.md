# docker

Docker
http://falbar.ru/article/vvedenie-v-kontejnery-vm-i-docker-dlya-novichkov - ОЧЕНЬ КРУТАЯ СТАТЬЯ
https://habr.com/ru/company/ruvds/blog/439978/ ТОЖЕ АТАС ПРОСТО БОМБА
https://docs.docker.com/engine/docker-overview/
https://cloud.croc.ru/blog/byt-v-teme/docker-machine/
https://habr.com/ru/company/flant/blog/336654/
https://github.com/VBrazhnik/docker-1/wiki/How-to-Docker
https://github.com/Lucimore/docker-1/blob/master/00_how_to_docker/31

1. Создайте виртуальную машину с Docker-машиной, используя драйвер virtualbox, и с именем Char.
https://javarush.ru/groups/posts/2161-pervoe-znakomstvo-s-docker
docker-machine create --driver virtualbox Char

2. Получите IP-адрес виртуальной машины Char.
docker-machine ls
docker-machine ip Char

3. Определите переменные, необходимые вашей виртуальной машине Char, в общем окружении вашего терминала, чтобы вы могли безошибочно выполнить команду docker ps. Вы должны исправить все четыре переменные среды с помощью одной команды, и вам не разрешено использовать встроенную функцию вашей оболочки для установки этих переменных вручную.
Эта команда сама подсказывает, как применить эти переменные в вашем окружении, чтобы настроить клиента:
   docker-machine env Char

После выполнения данной команды, клиент docker будет настроен на работу с Docker хостом Char.
eval $(docker-machine env Char)

4. Получите контейнер hello-world из Docker Hub, где он доступен.
docker pull hello-world

Pull (выгрузка из реестра) образа

Чекнуть docker image ls
5. Запустите контейнер hello-world и убедитесь, что он печатает приветственное сообщение, а затем покидает его.
docker run hello-world

6. Запустите контейнер nginx, доступный в Docker Hub, в качестве фоновой задачи. Это
должен иметь имя overlord, иметь возможность перезагрузки самостоятельно и иметь свой порт 80
подключен к порту 5000 чар. Вы можете проверить, что ваш контейнер функционирует
правильно, посетив
http: // <ip-de-char>: 5000 в вашем веб-браузере.
docker run -d -p 5000:80 --name overlord --restart=always nginx

-d, --detach- Выполнить контейнер в фоновом режиме и печати контейнера ID.
--name string - Присвойте имя контейнеру.
-p, --publish list- Опубликуйте порт (ы) контейнера для хоста.
Используйте Docker --restartдля указания политики перезапуска контейнера. Политика перезапуска определяет, будет ли демон Docker перезапускать контейнер после выхода. Docker поддерживает следующие политики перезапуска:
политика	Результат
no	Не перезапускать контейнер автоматически при его выходе. Это по умолчанию.
on-failure[:max-retries]	Перезапуск, только если контейнер выходит с ненулевым состоянием выхода. При желании ограничьте количество повторных попыток перезапуска попыток демона Docker.
unless-stopped	Перезапускайте контейнер, если он явно не остановлен или сам Docker не остановлен или перезапущен.
always	Всегда перезапускайте контейнер независимо от состояния выхода. Когда вы указываете всегда, демон Docker будет пытаться перезапустить контейнер на неопределенный срок. Контейнер также всегда запускается при запуске демона, независимо от текущего состояния контейнера.

7. Получить внутренний IP-адрес контейнера overlord без запуска его оболочки ив одной команде.
docker inspect --format {{.NetworkSettings.IPAddress}} overlord

локальный ip-адрес контейнера-
Inspect - Возврат низкоуровневой информации об объектах Docker
--format , -f		Отформатируйте вывод, используя данный шаблон Go
--size , -s		Отображать общий размер файла, если тип является контейнером
--type		Вернуть JSON для указанного типа

https://docs.docker.com/engine/reference/commandline/inspect/
https://fooobar.com/questions/14736/how-to-get-a-docker-containers-ip-address-from-the-host

8. Запустите оболочку из альпийского контейнера и убедитесь, что вы можете взаимодействовать
непосредственно с контейнером через ваш терминал, и что контейнер удаляет себя после выполнения оболочки.
docker run -it --rm alpine /bin/sh

1. /bin/ash - Ash (Almquist Shell), предоставленный BusyBox
2. --rm Автоматически удалять контейнер, когда он выходит (docker run --help)
3. -i Интерактивный режим (удерживайте STDIN открытым, даже если он не подключен)
4. -t Выделить псевдотематику


9. Из оболочки контейнера debian установите через менеджер пакетов контейнера
все, что вам нужно, чтобы скомпилировать исходный код на C и поместить его в git-репозиторий
Конечно, убедитесь, что менеджер пакетов и пакеты уже в
Контейнер обновляется). Для этого упражнения вы должны указать только команды
запускаться прямо в контейнере.
docker run -it --rm debian
apt-get update
apt-get upgrade -y
apt install -y build-essential git

https://packages.ubuntu.com/xenial/build-essential

10. Создайте том с именем инкубаторий.
docker volume create --name hatchery

https://habr.com/ru/company/ruvds/blog/441574/
https://docs.docker.com/engine/reference/commandline/volume_create/

11. Перечислите все тома Docker, созданные на машине. Помнить. ОБЪЕМЫ
docker volume ls

12. Запустите контейнер MySQL в качестве фоновой задачи. Он должен быть в состоянии перезапустить самостоятельно в случае ошибки, и пароль root базы данных должен быть Kerrigan.
Вы также убедитесь, что база данных хранится в томе инкубатория, что контейнер непосредственно создает базу данных с именем zerglings и что сам контейнер называется spawning-pool.
docker run -d --name spawning-pool --restart=on-failure:10 -e MYSQL_ROOT_PASSWORD=Kerrigan -e MYSQL_DATABASE=zerglings -v hatchery:/var/lib/mysql mysql --default-authentication-plugin=mysql_native_password

docker run -d --name spawning-pool --restart=on-failure:10 -e MYSQL_ROOT_PASSWORD=Kerrigan -e MYSQL_DATABASE=zerglings -v hatchery:/var/lib/mysql mysql:5.7

-v 
Путь к файлу конфигурации 
/var/lib/mysql is the default directory where MySQL container writes its files.
MYSQL_ROOT_PASSWORD
Эта переменная является обязательной и указывает пароль, который будет установлен для rootучетной записи суперпользователя MySQL . В приведенном выше примере было установлено значение my-secret-pw.
MYSQL_DATABASE
Эта переменная является необязательной и позволяет указать имя базы данных, которая будет создана при запуске образа. Если был указан пользователь / пароль (см. Ниже), то этому пользователю будет предоставлен доступ суперпользователя ( соответствующийGRANT ALL ) к этой базе данных.

https://hub.docker.com/_/mysql
docker exec -it spawning-pool mysql -uroot -p
13. Напечатайте переменные среды контейнера spawning-pool одной командой, чтобы убедиться, что вы правильно настроили свой контейнер.
docker inspect -f '{{.Config.Env}}' spawning-pool
14. Запустите контейнер WordPress в качестве фоновой задачи, просто для удовольствия. Контейнер должен называться lair, его порт 80 должен быть привязан к порту 8080 виртуального
машина, и она должна быть в состоянии использовать контейнер нерестового пула в качестве службы базы данных. Вы можете попытаться получить доступ к логову на своей машине через веб-браузер, указав в качестве URL-адреса виртуальную машину.
Поздравляем, вы только что развернули функциональный сайт Wordpress двумя командами!
docker run -d --name lair -p 8080:80 --link spawning-pool:mysql -e WORDPRESS_DB_HOST=spawning-pool:3306 -e WORDPRESS_DB_PASSWORD=Kerrigan -e WORDPRESS_DB_NAME=zerglings wordpress
docker restart lair
https://hub.docker.com/_/wordpress

15. Запустите контейнер phpmyadmin в качестве фоновой задачи. Он должен называться roach-warden, его порт 80 должен быть связан с портом 8081 виртуальной машины, и он должен иметь возможность исследовать базу данных, хранящуюся в контейнере нерестового пула.
docker run --name roach-warden -p 8081:80 -d --link spawning-pool:db phpmyadmin/phpmyadmin

docker restart roach-warden

https://hub.docker.com/r/phpmyadmin/phpmyadmin

16. Просмотрите журналы контейнера нереста в реальном времени, не запуская его оболочку.
docker logs -f spawning-pool

17. Отобразите все активные в данный момент контейнеры на виртуальной машине Char.
docker ps

18. Перезапустите контейнер «Оверлорд».
docker restart overlord

19. Запустите контейнер с именем Abathur. Это будет контейнер Python, версия 2-slim, его папка / root будет связана с папкой HOME на вашем хосте, а ее порт 3000 будет связан с портом 3000 вашей виртуальной машины.
Вы настроите этот контейнер так, чтобы вы могли использовать микро-фреймворк Flask в его последней версии. Вы позаботитесь о том, чтобы Flask обслуживал html-страницу с тегами «Hello World» с тегами <h1>. Вы проверите, правильно ли настроен ваш контейнер, обратившись через curl или веб-браузер к IP-адресу вашей виртуальной машины через порт 3000. Вы также перечислите все необходимые команды в своем хранилище.
docker run -dit --name Abathur -p 3000:3000 -v ~/:/root python:2-slim
docker exec Abathur pip install Flask
echo 'from flask import Flask\napp = Flask(__name__)\n@app.route("/")\ndef hello_world():\n\treturn "<h1>Hello, World!</h1>"' > ~/hello.py
docker exec -e FLASK_APP=/root/hello.py Abathur flask run --host=0.0.0.0 --port 3000


Команда docker execзапускает новую команду в работающем контейнере.
Команда, запущенная с использованием, docker execзапускается только во время работы основного процесса контейнера ( PID 1) и не перезапускается при перезапуске контейнера.
-e Установка переменных среды
-d фоновый режим
-i —interactive Благодаря этому флагу поток STDIN поддерживается в открытом состоянии даже если контейнер к STDIN не подключён.
-t —tty. Благодаря этому флагу выделяется псевдотерминал, который соединяет используемый терминал с потоками STDIN и STDOUT контейнера

docker restart Abathur

https://docs.docker.com/engine/reference/commandline/exec/
https://flask.palletsprojects.com/en/1.1.x/quickstart/
https://hub.docker.com/_/python?tab=description&page=1&name=slim
20. Создайте локальный рой, виртуальная машина Char должна быть его менеджером.
https://blog.iteducenter.ua/sysadmin-basics/osnovy-raboty-s-docker-swarm/
docker swarm init --advertise-addr $(docker-machine ip Char)
Запустите docker node ls команду для просмотра информации об узлах
https://docs.docker.com/engine/reference/commandline/swarm_init/
21. Создайте еще одну виртуальную машину с докер-машиной, используя драйвер virtualbox, и назовите ее Aiur.
docker-machine create --driver virtualbox Aiur
22. Превратите Айура в раба местного роя, в котором Чар является лидером (команда для захвата контроля над Айуром не запрашивается).
https://docs.docker.com/engine/swarm/swarm-tutorial/add-nodes/
docker-machine ssh Aiur "docker swarm join --token $(docker swarm join-token worker -q) $(docker-machine ip Char):2377"
https://docs.docker.com/engine/reference/commandline/swarm_join-token/
23. Создайте внутреннюю сеть типа оверлея, которую вы назовете overmind.
https://docs.docker.com/engine/reference/commandline/network_create/
https://docs.docker.com/network/overlay/
https://docs.docker.com/network/bridge/
docker network create -d overlay overmind
docker network ls
Overlay Сетевой драйвер создает распределенную сеть между несколькими узлами демона Докера. Эта сеть расположена поверх (перекрывает) хост-специфичные сети, позволяя подключенным к ней контейнерам (включая сервисные контейнеры Swarm) безопасно общаться. Docker прозрачно обрабатывает маршрутизацию каждого пакета к правильному хосту демона Docker и правильному контейнеру назначения.
24. Запустите СЕРВИС rabbitmq, который будет называться orbital-command. Вы должны определить конкретного пользователя и пароль для службы RabbitMQ, они могут быть любыми. Эта услуга будет в сети overmind.
Service - https://habr.com/ru/company/ruvds/blog/439978/
https://docs.docker.com/engine/reference/commandline/service_create/
https://hub.docker.com/_/rabbitmq
docker service create -d --network overmind --name orbital-command -e RABBITMQ_DEFAULT_USER=cdoreah -e RABBITMQ_DEFAULT_PASS=cdoreah rabbitmq

RabbitMQ — это платформа позволяющая обмениваться сообщениями. Что может быть в сообщении — решать только тебе. Обмениваться можно как на одном сервере, так и с одного на другой. Это отличный способ масштабирования, так как с хорошо настроенным RabbitMQ мы можем просто подключать новые сервера, настроив на них нужное ПО и прописав конфигурацию, а RabbitMQ будет сам делегировать работу между всеми серверами. 

25. Перечислите все услуги местного роя.
docker service ls


26. Запустите службу 42school / engineering-bay в двух репликах и убедитесь, что служба работает правильно (см. Документацию, предоставленную на hub.docker.com). Этот сервис будет называться Engineering-Bay и будет находиться в сети overmind.
docker service create -d --network overmind --name engineering-bay --replicas 2 -e OC_USERNAME=cdoreah -e OC_PASSWD=cdoreah 42school/engineering-bay

https://hub.docker.com/r/42school/engineering-bay

27. Получить в режиме реального времени журналы одной из задач службы технической поддержки.
docker service logs -f engineering-bay
docker service logs -f $(docker service ps -f "name=engineering-bay.1" engineering-bay -q)
docker service logs -f engineering-bay | grep "engineering-bay.1"

28. ... Черт возьми, группа зергов нападает на орбитальные команды, и остановка службы инженерного отсека совсем не поможет ... Вы должны отправить группу морских пехотинцев, чтобы уничтожить злоумышленников. Запустите 42school / marine-squad в двух репликах и убедитесь, что служба работает должным образом (см. Документацию, предоставленную на hub.docker.com). Этот сервис будет называться ... морские пехотинцы и будет находиться в сети overmind.
docker service create -d --network overmind --name marines --replicas 2 -e OC_USERNAME=cdoreah -e OC_PASSWD=cdoreah 42school/marine-squad

29. Показать все задачи службы морской пехоты.
docker service ps marines


30. Увеличьте количество копий службы морской пехоты до двадцати, потому что морских пехотинцев никогда не хватит для уничтожения зергов. (Не забудьте взглянуть на задачи и журналы сервиса, как вы увидите, это весело.)
https://docs.docker.com/engine/reference/commandline/service_scale/
docker service scale -d marines=20

31. Принудительно выйдите и удалите все службы на локальном рое в одну команду.
https://docs.docker.com/engine/reference/commandline/service_rm/
https://docs.docker.com/engine/reference/commandline/swarm_leave/
docker service rm $(docker service ls -q)

32. Принудительно завершить работу и удалить все контейнеры (независимо от их статуса) одной командой.
https://docs.docker.com/engine/reference/commandline/ps/
docker rm -f $(docker ps -a -q)

rm          Remove one or more containers
-q, --quiet           Only display IDs
-f, --force     Force the removal of a running container (uses SIGKILL)

33. Удалите все образы контейнеров, хранящиеся на виртуальной машине Char, также одной командой.
https://docs.docker.com/engine/reference/commandline/rmi/
https://docs.docker.com/engine/reference/commandline/images/
docker rmi $(docker images -a -q)

rmi         Remove one or more images
-q, --quiet           Only display IDs

34. Удалите виртуальную машину Aiur без использования rm -rf.
https://docs.docker.com/v17.12/machine/reference/rm/
docker-machine rm -y Aiur

-y - Предполагается автоматическое да, чтобы продолжить удаление, не запрашивая дальнейшего подтверждения пользователя.


ЧАСТЬ ДВА
0
https://habr.com/ru/company/ruvds/blog/439980/
FROM alpine
RUN apk update && apk upgrade && apk add vim
ENTRYPOINT vim


Запуск
docker build -t alpine_vim .
docker run --name Alpine_Vim -it alpine_vim

FROM — задаёт базовый (родительский) образ.
RUN — выполняет команду и создаёт слой образа. Используется для установки в контейнер пакетов.
ENTRYPOINT — предоставляет команду с аргументами для вызова во время выполнения контейнера. Аргументы не переопределяются.



1
https://files.teamspeak-services.com/releases

https://hub.docker.com/_/teamspeak
https://community.vscale.io/hc/ru/community/posts/209453545-%D0%A3%D1%81%D1%82%D0%B0%D0%BD%D0%BE%D0%B2%D0%BA%D0%B0-%D0%B8-%D0%BF%D0%B5%D1%80%D0%B2%D0%B8%D1%87%D0%BD%D0%B0%D1%8F-%D0%BD%D0%B0%D1%81%D1%82%D1%80%D0%BE%D0%B9%D0%BA%D0%B0-TeamSpeak-%D0%BD%D0%B0-%D1%81%D0%B5%D1%80%D0%B2%D0%B5%D1%80%D0%B5-%D1%81-Ubuntu-16-04
https://www.vultr.com/docs/how-to-install-teamspeak-3-server-on-debian-9-stretch 
FROM debian
ENV TS3SERVER_LICENSE=accept
EXPOSE 9987/udp 10011 30033
RUN apt-get update -y && apt-get upgrade -y && apt-get install wget -y && apt-get install bzip2 -y && wget https://files.teamspeak-services.com/releases/server/3.10.2/teamspeak3-server_linux_amd64-3.10.2.tar.bz2 && tar xvf teamspeak3-server_linux_amd64-3.10.2.tar.bz2
WORKDIR teamspeak3-server_linux_amd64
CMD sh ts3server_minimal_runscript.sh
Запуск
docker build -t ex01 .
docker run -it --name teamspeak --rm -p 9987:9987/udp -p 10011:10011 -p 30033:30033 TS3SERVER_LICENSE=accept ex01
ENV — устанавливает постоянные переменные среды.
EXPOSE — указывает на необходимость открыть порт.
WORKDIR — задаёт рабочую директорию для следующей инструкции.
CMD — описывает команду с аргументами, которую нужно выполнить когда контейнер будет запущен. Аргументы могут быть переопределены при запуске контейнера. В файле может присутствовать лишь одна инструкция CMD.
2
https://losst.ru/ustanovka-ruby-on-rails-v-ubuntu-18-04
Ruby on Rails (RoR) — фреймворк, написанный на языке программирования Ruby, реализует архитектурный шаблон Model-View-Controller для веб-приложений, а также обеспечивает их интеграцию с веб-сервером и сервером баз данных. Является открытым программным обеспечением и распространяется под лицензией MIT.
каркас веб-приложений
Инструкция ONBUILD добавляет к изображению инструкцию триггера, которая должна быть выполнена позже, когда изображение используется в качестве основы для другой сборки. Триггер будет выполнен в контексте нисходящей сборки, как если бы он был вставлен сразу после инструкции FROM в нисходящем Dockerfile.
RubyGems - менеджер пакетов Ruby, поставляемый в виде инструмента командной строки gem. Он автоматически устанавливается при установке Ruby в системе. 
https://ru.wikipedia.org/wiki/RubyGems
RubyGems — фрэймворк для установки и запаковки Ruby библиотек и приложений.
gem — пакет(файл) с библиотекой или приложением. Имеет стандартизированный вид и расположен в хранилище в сети.
gem command tool — RubyGems предоставляет утилиту «gem» для работы с gem пакетами из командной строки. Она интегрирована с Ruby и позволяет получать доступ к установленным gem'ам как к библиотекам.
https://habr.com/ru/post/85201/
Что такое Bundler?
Это менеджер для управления зависимостями gem'ов в ruby приложениях. Эта утилита позволяет легко устанавливать необходимые gem'ы для вашего приложения, при этом вовсе не зависеть от установленных в системе. Если вы использовали Rails для своих разработок, то вы вспомните, как задавали зависимости gem'ов c помощью config.gem в enviroment.rb, Bundler решает эту задачу намного удобнее и проще. Его включили в Rails 3.0 по умолчанию и теперь, именно он используется для управления зависимостями gem’ов в данной версии фреймворка. Эту утилиту можно использовать для любого ruby фреймворка.
Как тока задали все нужные gem'ы, нужно выполнить команду:
bundle install
Эта команда решит все зависимости и установит недостающие gem'ы.
https://losst.ru/ustanovka-ruby-on-rails-v-ubuntu-18-04#_ruby_on_rails_Ubuntu_1804
Ruby on Rails требует времени для компиляции конвейера ресурсов Rails на JavaScript. Для разработчиков на Rails в Ubuntu Linux лучше всего установить и использовать Nodejs в качестве среды выполнения Javascript. Добавим репозиторий nodejs Node в систему.
Rake - это утилита, встроенная в Ruby и Rails , которая обеспечивает эффективный способ управления изменениями базы данных. Вы можете легко перенести изменения базы данных на серверы, используя только командную строку!
rake db:migrate
Миграции устанавливают таблицы в базе данных. Когда вы запускаете команду переноса, она ищет db/migrate/любые файлы ruby и выполняет их, начиная с самой старой. В начале каждого имени файла миграции есть метка времени .
rake db:seed У нас всегда есть данные по умолчанию, которые мы хотим иметь в нашем приложении для целей тестирования. Команда seed существует для автоматизации этого процесса.
Пример : создайте пользователя-администратора и сохраните его данные в db/seed.rbфайле. При запуске rake db:seedон загрузит все данные администратора в ваше приложение.
https://kolosek.com/rake-db-commands/
FROM ruby:2.4.1
RUN apt-get update -y && apt-get upgrade -y && apt-get install -y build-essential libpq-dev nodejs sqlite3
ONBUILD COPY myapp /opt/app
ONBUILD WORKDIR /opt/app
ONBUILD EXPOSE 3000
ONBUILD RUN bundle install
ONBUILD RUN rake db:migrate
ONBUILD RUN rake db:seed
docker build -t ft-rails:on-build .
docker build -t ex02 .
docker run -d -p 3000:3000  ex02
3
Вводная часть
https://1cloud.ru/help/linux/ustanovka-gitlab-na-ubuntu
https://losst.ru/ustanovka-gitlab-v-ubuntu-18-04#_GitLab_Ubuntu_1804
Пакеты официальные
https://packages.gitlab.com/gitlab/gitlab-ce/packages/debian/buster/gitlab-ce_12.7.2-ce.0_amd64.deb
Запуск
docker build -t ex03 .
docker run -it --rm -p 80:80 -p 22:22 -p 443:443 --privileged ex03
--privileged		Дайте расширенные привилегии этому контейнеру
https://startpack.ru/application/gitlab
DEBIAN_FRONTEND=noninteractive - игнорирует вопросы конфигурации 
-yq - отвечает да и не выводит сообщения на экран
docker run -it --rm -p 80:80 -p 8022:22 -p 443:443 --privileged ubuntu /bin/sh
/opt/gitlab/embedded/bin/runsvdir-start &
Resolve problem:
https://stackoverflow.com/questions/27983295/gitlab-autostart-on-boot
https://docs.gitlab.com/omnibus/common_installation_problems/
FROM ubuntu
RUN apt-get update && \
    apt-get upgrade -y && \
	DEBIAN_FRONTEND=noninteractive apt-get install -yq --no-install-recommends \
	ca-certificates \
	openssh-server \
	curl \
	tzdata
# Download & Install GitLab
# Сценарий настроит Ваш сервер для поддержки репозиториев GitLab. Затем Вы можете установить актуальную версию приложения GitLab:
RUN curl -s https://packages.gitlab.com/install/repositories/gitlab/gitlab-ce/script.deb.sh | bash
RUN apt-get install gitlab-ce -y
# Create SSL certificate
RUN mkdir /etc/gitlab/ssl/ && chmod 700 /etc/gitlab/ssl/
RUN openssl genrsa -out /etc/gitlab/ssl/git.example.com.key 2048
RUN openssl req -new -key /etc/gitlab/ssl/git.example.com.key -out /etc/gitlab/ssl/git.example.com.csr \
	-subj "/C=RU/ST=Moscow/L=Moscow/O=21-school/OU=42/CN=git.example.com"
RUN openssl x509 -in /etc/gitlab/ssl/git.example.com.csr -out /etc/gitlab/ssl/git.example.com.crt -req -signkey /etc/gitlab/ssl/git.example.com.key -days 365
RUN echo "letsencrypt['enable'] = false" >> /etc/gitlab/gitlab.rb && \
sed -i "s/^external_url.*/external_url \'https:\/\/git.example.com\'/" /etc/gitlab/gitlab.rb
# Expose web & ssh
EXPOSE 443 80 22
ENTRYPOINT (/opt/gitlab/embedded/bin/runsvdir-start &) && \
	gitlab-ctl reconfigure && \
	service ssh start && \
	tail -f /dev/null
Ssh
service ssh start
git clone ssh://git@localhost/root/lala.git
Https
git clone 
Ssl
https://losst.ru/sozdanie-sertifikata-openssl
https://docs.gitlab.com/omnibus/settings/ssl.html
https://copdips.com/2018/09/setup-https-for-gitlab.html
1)
mkdir /etc/gitlab/ssl/ && chmod 700 /etc/gitlab/ssl/
Создайте новый закрытый ключ:
openssl genrsa -out /etc/gitlab/ssl/git.example.com.key 2048
создайте временный самоподписанный сертификат:
openssl req -new -x509 -nodes -days 42 -key /etc/gitlab/ssl/git.example.com.key -out /etc/gitlab/ssl/git.example.com.crt \
			-subj "/C=RU/ST=Moscow/L=Moscow/O=21-school/OU=42/CN=git.example.com/emailAddress=sweettpickle@gmail.com" 
echo "letsencrypt['enable'] = false" >> /etc/gitlab/gitlab.rb && \
sed -i "s/^external_url.*/external_url \'https:\/\/git.example.com\'/" /etc/gitlab/gitlab.rb
2)
https://futurestud.io/tutorials/how-to-run-gitlab-with-self-signed-ssl-certificate
https://docs.bitnami.com/aws/apps/gitlab/administration/create-ssl-certificate-nginx/
mkdir /etc/gitlab/ssl/ && chmod 700 /etc/gitlab/ssl/
Создайте новый закрытый ключ:
openssl genrsa -out /etc/gitlab/ssl/git.example.com.key 2048
Создать запрос на сертификат:
openssl req -new -key /etc/gitlab/ssl/git.example.com.key -out  /etc/gitlab/ssl/git.example.com.csr \
	-subj "/C=RU/ST=Moscow/L=Moscow/O=21-school/OU=42/CN=git.example.com/emailAddress=sweettpickle@gmail.com"
Пока сертификат не получен, создайте временный самоподписанный сертификат:
openssl x509 -in  /etc/gitlab/ssl/git.example.com.csr -out  /etc/gitlab/ssl/git.example.com.crt -req -signkey  /etc/gitlab/ssl/git.example.com.key -days 365
echo "letsencrypt['enable'] = false" >> /etc/gitlab/gitlab.rb && \
sed -i "s/^external_url.*/external_url \'https:\/\/git.example.com\'/" /etc/gitlab/gitlab.rb
https://confluence.atlassian.com/fishkb/unable-to-clone-git-repository-due-to-self-signed-certificate-376838977.html
Ошибка при клонировании:
Отключите  проверку Git SSL на сервере, на котором размещен Fisheye / Crucible, с помощью следующих команд:
git config --global http.sslVerify false
https://localhost
git clone https://localhost/root/lala.git test
git clone ssh://git@localhost/root/lala.git
Бонусы
docker run --rm -v $(pwd)/vol:/data/:rw 01
