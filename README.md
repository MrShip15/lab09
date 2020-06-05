[![Build Status](https://travis-ci.org/MrShip15/lab04.svg?branch=master)](https://travis-ci.org/MrShip15/lab04)

## Laboratory work IV

Данная лабораторная работа посвещена изучению систем непрерывной интеграции на примере сервиса **Travis CI**

```sh
$ open https://travis-ci.org
```

## Tasks

- [x] 1. Авторизоваться на сервисе **Travis CI** с использованием **GitHub** аккаунта
- [x] 2. Создать публичный репозиторий с названием **lab04** на сервисе **GitHub**
- [x] 3. Ознакомиться со ссылками учебного материала
- [x] 4. Включить интеграцию сервиса **Travis CI** с созданным репозиторием
- [x] 5. Получить токен для **Travis CLI** с правами **repo** и **user**
- [x] 6. Получить фрагмент вставки значка сервиса **Travis CI** в формате **Markdown**
- [x] 7. Выполнить инструкцию учебного материала

## Tutorial
Создание переменных среды и установка их значений
```sh
$ export GITHUB_USERNAME=MrShip15
$ export GITHUB_TOKEN=*************************
```
Начало работы в каталоге workspace
```sh
# Переход в  рабочую директорию
$ cd ${GITHUB_USERNAME}/workspace
$ pushd . # Сохранение текущего каталога в стек
# Активация Node.js
$ source scripts/activate
```
Установка пакетного менеджера rvm (Программа для управления версиями Ruby) и пакета travis.
```sh
$ \curl -sSL https://get.rvm.io | bash -s -- --ignore-dotfiles # установка программы RVM
Turning on ignore dotfiles mode.

Downloading https://github.com/rvm/rvm/archive/master.tar.gz
Installing RVM to /Users/evgengrmit/.rvm/
# Добавляем в скрипт activate, команду который добавляет каталог.rvm/scripts/rvm к переменной среды PATH
$ echo "source $HOME/.rvm/scripts/rvm" >> scripts/activate
# Повторяем скрипт для активации rvm
$ . scripts/activate
$ rvm autolibs disable # запрет установки сопутствующих библиотек
$ brew install ruby # установка интерпретатора языка Ruby
$ gem install travis # установка travis из пакетного менеджера ruby
Successfully installed travis-1.8.13
```
Настройка git-репозитория **lab04** для работы
```sh
$ git clone https://github.com/${GITHUB_USERNAME}/lab03 projects/lab04
$ cd projects/lab04
$ git remote remove origin
$ git remote add origin https://github.com/${GITHUB_USERNAME}/lab04
```
Создание файла `.travis.yml`
```sh
# Установка языка программирования
$ cat > .travis.yml <<EOF
language: cpp
EOF
```
Установка пользовательского сценария запуска
```sh
# Установка настроек СMake, запуск и исполнение
$ cat >> .travis.yml <<EOF

script:
- cmake -H. -B_build -DCMAKE_INSTALL_PREFIX=_install
- cmake --build _build
- cmake --build _build --target install
EOF
```

```sh
# Итерации конфигурирования источников и пакетов
# Установка д0полнительных исполняемых файлов и пакетов
$ cat >> .travis.yml <<EOF

addons:
  apt:
    sources:
      - george-edison55-precise-backports
    packages:
      - cmake
      - cmake-data
EOF
```
Логирование по полученому токену для **Travis CI**
```sh
$ travis login --github-token ${GITHUB_TOKEN}
Successfully logged in as Evgengrmit!
```
Проверка `.travis.yml` на ошибки
```sh
$ travis lint
Hooray, .travis.yml looks valid :)
```
Вставка значков с Build Status для `travis-ci.org`
```sh
[![Build Status](https://travis-ci.org/MrShip15/lab04.svg?branch=master)](https://travis-ci.org/MrShip15/lab04)
```
Запись изменений в удаленный репозиторий
```sh
$ git add .travis.yml
$ git add README.md
$ git commit -m"added CI"
$ git push origin master
```
Команды **travis** в `bash`
```sh
$ travis lint
$ travis accounts
$ travis sync
$ travis repos
$ travis enable
$ travis whatsup
$ travis branches
$ travis history
$ travis show
```

## Report
Создание отчета по ЛР № 4
```sh
$ popd
$ export LAB_NUMBER=04
$ git clone https://github.com/tp-labs/lab${LAB_NUMBER} tasks/lab${LAB_NUMBER}
$ mkdir reports/lab${LAB_NUMBER}
$ cp tasks/lab${LAB_NUMBER}/README.md reports/lab${LAB_NUMBER}/REPORT.md
$ cd reports/lab${LAB_NUMBER}
$ edit REPORT.md
$ gist REPORT.md
```

## Links

- [Travis Client](https://github.com/travis-ci/travis.rb)
- [AppVeyour](https://www.appveyor.com/)
- [GitLab CI](https://about.gitlab.com/gitlab-ci/)

```
Copyright (c) 2015-2020 The ISC Authors
```
