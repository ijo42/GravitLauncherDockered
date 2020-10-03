# GravitLauncherDockered
Что ты здесь делаешь?? уходи скорее, пока спишь по ночам спокойно!

Сегодня мы.. мы ставим [GravitLauncher](https://github.com/GravitLauncher) наиболее простым способом. Docker.

Что нам для этого понадобится..? Серверная машина (желательно, Linux дистрибутив), куда потребуется поставить Docker

`curl -fsSL https://get.docker.com -o get-docker.sh && sudo sh get-docker.sh`

Что нам нужно знать.. я собрал докер-имейдж, в котором содержится образ BellSoft Java 11, с минимальным кол-вом зависимостей, что получилось благодаря использованию Alpine linux и выпила большинства java module.
## Самый простой пример запуска лаунчсервера в режиме CLI
```
docker run -it -p 9274:9274 ijo42/launcher
```
Что мы тут делаем? 

`docker run` - это запуск докер-контейнера

`-it` - два флага, необходимые для коннекта к запущенному контейнеру

`-p 9274:9274` - маппим порт из контейнера наружу, что бы можно было подключиться к лаунчсерверу из вне

`ijo42/launcher` - название готового докер-имейджа

В данном примере мы можем запустить лаунчсервер, и даже слегка использовать для отладочных целей, но не более.

## Создание кастомного докер-имейджа
Зачем? Если у вас есть статичные файлы, можно смонтировать их в имейдж и забыть об их сущестовании и жить дальше. К примеру, можно смонтировать ключи, рантайм, конфиги

Для начала создаем папку `ls` и переносим в неё все, что хотим вмонтировать. Далее, склонируем репо и запустим скрипт билда:
```
git clone https://github.com/ijo42/GravitLauncherDockered/
cd GravitLauncherDockered/samples/
sh build.sh
```
У вас потребуют имя юзера, от имени которого будут происходить операции, его вы должны создать отдельно *root под запретом!*

Казалось бы, создали имейдж.. а что дальше? А дальше я рекомендую использовать docker-compose (пакет, который необходимо установить отдельно)

*Если вы воспользовались скриптом билда, можно заменить 'build: .' на 'image: local/launchserver'`

Можете воспользоваться готовым [docker-compose файлом](https://github.com/ijo42/GravitLauncherDockered/blob/master/samples/docker-compose.yml)

А мы тем временем разберём его содержимое.

`build: .` - билд происходит из докерфайла текущей директории

`tty`,`stdin_open` - флаги, необходимые для работы аттача

`mem_limit` - hard-limit для запускаемого контейнера

`ports` - маппим порты для доступа из вне

`volumes` - самый интересный пункт файла, в котором задаются динамические файлы, такие как папка updates, profiles и различные конфигурации, требующие вмешательства

`restart: always` - задаем рестарт при выключении контейнера

После заполнения docker-compose.yml мы со смелым выражением лица берем и запускам сборку имейджа

`docker-compose build`, после успешной сборки, `docker-compose -f docker-compose.yml up -d`

Готово! Ваш лаунчсервер готов принимать первых игроков, а если захотите подключиться к CLI, можно воспользоваться командой `docker attach launchserver`, а для перезагрузки `docker restart launchserver`. Возрадуйтесь докеру!

Что стоит отметить отдельно, не стоит использовать в конфигурации лаунчсервера такие слова, как localhost, в связи с особенностями архитектуры докера, рекомендую использовать JSON или Request методы
