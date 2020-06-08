[![Build Status](https://travis-ci.org/MrShip15/lab09.svg?branch=master)](https://travis-ci.org/MrShip15/lab09)
## Laboratory work IX
<a href="https://yandex.ru/efir/?stream_id=vYrKRcFKi46o"><img src="https://raw.githubusercontent.com/tp-labs/lab09/master/preview.png" width="640"/></a>

Данная лабораторная работа посвещена изучению процесса создания артефактов на примере **Github Release**

```sh
$ open https://help.github.com/articles/creating-releases/
```

## Tasks

- [x] 1. Создать публичный репозиторий с названием **lab09** на сервисе **GitHub**
- [x] 2. Ознакомиться со ссылками учебного материала
- [x] 3. Получить токен для доступа к репозиториям сервиса **GitHub**
- [x] 4. Выполнить инструкцию учебного материала
- [x] 5. Составить отчет и отправить ссылку личным сообщением в **Slack**

## Tutorial
Создание переменных среды и установка их значений.
```sh
$ export GITHUB_TOKEN=**********************
$ export GITHUB_USERNAME=MrShip15
$ export PACKAGE_MANAGER=apt
$ export GPG_PACKAGE_NAME=gpg
```
Установка утилиты **xclip**, которая представляет доступ к буферу обмена X из командной строки.
```sh
# for *-nix system
$ $PACKAGE_MANAGER install xclip
$ alias gsed=sed
$ alias pbcopy='xclip -selection clipboard'
# Связывание команды pbpaste с чтением данных из секции clipboard
$ alias pbpaste='xclip -selection clipboard -o'
```
Установка приложения командной строки **github-release**, для работы с релизами на **Github**.
```sh
$ cd ${GITHUB_USERNAME}/workspace
$ pushd . # Сохранение текущего каталога в стек
$ source scripts/activate
$ go get github.com/aktau/github-release
```
Настройка git-репозитория **lab09** для работы.
```sh
$ git clone https://github.com/${GITHUB_USERNAME}/lab08 projects/lab09
$ cd projects/lab09
$ git remote remove origin
$ git remote add origin https://github.com/${GITHUB_USERNAME}/lab09
```
Замена всех упоминаний `lab08` на `lab09`.
```sh
$ gsed -i "" 's/lab08/lab09/g' README.md
```
Установка и работа с программой **GPG** для шифрования информации и создания электронных цифровых подписей.
```sh
$ $PACKAGE_MANAGER install ${GPG_PACKAGE_NAME}
$ gpg --list-secret-keys --keyid-format
$ gpg --full-generate-key
$ gpg --list-secret-keys --keyid-format LONG
$ gpg -K ${GITHUB_USERNAME}
$ GPG_KEY_ID=$(gpg --list-secret-keys --keyid-format LONG | grep ssb | tail -1 | awk '{print $2}' | awk -F'/' '{print $2}')
$ GPG_SEC_KEY_ID=$(gpg --list-secret-keys --keyid-format LONG | grep sec | tail -1 | awk '{print $2}' | awk -F'/' '{print $2}')
$ gpg --armor --export ${GPG_KEY_ID} | pbcopy
$ pbpaste
$ open https://github.com/settings/keys
$ git config user.signingkey ${GPG_SEC_KEY_ID}
$ git config gpg.program gpg
```
Создание скрипта дя добавления сообщения к тегу
```sh
$ test -r ~/.zsh_profile && echo 'export GPG_TTY=$(tty)' >> ~/.zsh_profile
$ echo 'export GPG_TTY=$(tty)' >> ~/.profile
```
Генерация пакета с расширением `.tar.gz`
```sh
$ cmake -H. -B_build -DCPACK_GENERATOR="TGZ"
$ cmake --build _build --target package
```
Инициализация проекта на **Travis CI**
```sh
$ travis login --auto --pro
$ travis enable --pro
```
Создание тега для коммита
```sh
$ git tag -s v0.12.0.0
$ git tag -v v0.1.0.0
$ git show v0.1.0.0
$ git push origin master --tags
```
Создание релиза
```sh
$ github-release --version
github-release v0.8.1
$ github-release info -u ${GITHUB_USERNAME} -r lab09
$ github-release release \
    --user ${GITHUB_USERNAME} \
    --repo lab09 \
    --tag v0.1.0.0 \
    --name "libprint" \
    --description "my first release"
```
Добавление артефакта с указанием ОС и архитектуры, на которых происходила компиляция библиотеки
```sh
$ export PACKAGE_OS=`uname -s` PACKAGE_ARCH=`uname -m`
$ export PACKAGE_FILENAME=print-${PACKAGE_OS}-${PACKAGE_ARCH}.tar.gz
$ github-release upload \
    --user ${GITHUB_USERNAME} \
    --repo lab09 \
    --tag v0.1.0.0 \
    --name "${PACKAGE_FILENAME}" \
    --file _build/*.tar.gz
```

```sh
$ github-release info -u ${GITHUB_USERNAME} -r lab09tags:
$ wget https://github.com/${GITHUB_USERNAME}/lab09/releases/download/v0.1.0.0/${PACKAGE_FILENAME}
```

## Report
Создание отчета по ЛР № 9
```sh
$ popd
$ export LAB_NUMBER=09
$ git clone https://github.com/tp-labs/lab${LAB_NUMBER} tasks/lab${LAB_NUMBER}
$ mkdir reports/lab${LAB_NUMBER}
$ cp tasks/lab${LAB_NUMBER}/README.md reports/lab${LAB_NUMBER}/REPORT.md
$ cd reports/lab${LAB_NUMBER}
$ edit REPORT.md
$ gistup -m "lab${LAB_NUMBER}"
```

## Links

- [Create Release](https://help.github.com/articles/creating-releases/)
- [Get GitHub Token](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/)
- [Signing Commits](https://help.github.com/articles/signing-commits-with-gpg/)
- [Go Setup](http://www.golangbootcamp.com/book/get_setup)
- [github-release](https://github.com/aktau/github-release)

```
Copyright (c) 2015-2020 The ISC Authors
```
