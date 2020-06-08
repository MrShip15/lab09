[![Build Status](https://travis-ci.org/MrShip15/lab06.svg?branch=master)](https://travis-ci.org/MrShip15/lab06)
## Laboratory work V

<a href="https://yandex.ru/efir/?stream_id=vQw_LH0UfN6I"><img src="https://raw.githubusercontent.com/tp-labs/lab06/master/preview.png" width="640"/></a>

Данная лабораторная работа посвещена изучению фреймворков для тестирования на примере **GTest**

```sh
$ open https://github.com/google/googletest
```

## Tasks

- [x] 1. Создать публичный репозиторий с названием **lab06** на сервисе **GitHub**
- [x] 2. Выполнить инструкцию учебного материала
- [x] 3. Ознакомиться со ссылками учебного материала

## Tutorial
Создание переменных среды и установка их значений
```sh
$ export GITHUB_USERNAME=MrShip15
$ alias gsed=sed # for *-nix system
```

Начало работы в каталоге workspace
```sh
$ cd ${GITHUB_USERNAME}/workspace
$ pushd .
$ source scripts/activate
```
Настройка git-репозитория **lab06** для работы
```sh
$ git clone https://github.com/${GITHUB_USERNAME}/lab06 projects/lab06
$ cd projects/lab06
$ git remote remove origin
$ git remote add origin https://github.com/${GITHUB_USERNAME}/lab06
```
Подключение к репозиторию подмодуля **Google Test**, выбор версии с помощью переключения ветки
```sh
$ mkdir third-party
$ git submodule add https://github.com/google/googletest third-party/gtest
$ cd third-party/gtest && git checkout release-1.8.1 && cd ../..
$ git add third-party/gtest
$ git commit -m"added gtest framework"
```
Модифицируем CMakeList.txt
```sh
$ gsed -i "" '/option(BUILD_EXAMPLES "Build examples" OFF)/a\
option(BUILD_TESTS "Build tests" OFF)
' CMakeLists.txt
$ cat >> CMakeLists.txt <<EOF

if(BUILD_TESTS)
  enable_testing()
  add_subdirectory(third-party/gtest)
  file(GLOB \${PROJECT_NAME}_TEST_SOURCES tests/*.cpp)
  add_executable(check \${\${PROJECT_NAME}_TEST_SOURCES})
  target_link_libraries(check \${PROJECT_NAME} gtest_main)
  add_test(NAME check COMMAND check)
endif()
EOF
```
Создание теста
```sh
$ mkdir tests
$ cat > tests/test1.cpp <<EOF
#include <print.hpp>

#include <gtest/gtest.h>

TEST(Print, InFileStream)
{
  std::string filepath = "file.txt";
  std::string text = "hello";
  std::ofstream out{filepath};

  print(text, out);
  out.close();

  std::string result;
  std::ifstream in{filepath};
  in >> result;

  EXPECT_EQ(result, text);
}
EOF
```
Сборка проекта
```sh
$ cmake -H. -B_build -DBUILD_TESTS=ON
$ cmake --build _build
```
Запуск тестов
```sh
$ _build/check
$ cmake --build _build --target test -- ARGS=--verbose
```
Модифицируем `.travis.yml` и `README.md`
```sh
$ gsed -i "" 's/lab06/lab06/g' README.md
$ gsed -i "" 's/\(DCMAKE_INSTALL_PREFIX=_install\)/\1 -DBUILD_TESTS=ON/' .travis.yml
$ gsed -i "" '/cmake --build _build --target install/a\
- cmake --build _build --target test -- ARGS=--verbose
' .travis.yml
```
Проверка `.travis.yml`
```sh
$ travis lint
Hooray, .travis.yml looks valid :)
```
Фиксация изменений в репозитории
```sh
$ git add .travis.yml
$ git add tests
$ git add -p # запрос на изменение
$ git commit -m"added tests"
$ git push origin master
```
Логинимся и включаем TravisCI для репозитория
```sh
$ travis login --auto
$ travis enable
```
Создаем директорию и делаем скриншот с задержкой в 20 сек
```sh
$ mkdir artifacts
$ screencapture -T 20 artifacts/screenshot.png
```

## Report
Создание отчета по ЛР № 5
```sh
$ popd
$ export LAB_NUMBER=05
$ git clone https://github.com/tp-labs/lab${LAB_NUMBER} tasks/lab${LAB_NUMBER}
$ mkdir reports/lab${LAB_NUMBER}
$ cp tasks/lab${LAB_NUMBER}/README.md reports/lab${LAB_NUMBER}/REPORT.md
$ cd reports/lab${LAB_NUMBER}
$ edit REPORT.md
$ gist REPORT.md
```

## Links

- [C++ CI: Travis, CMake, GTest, Coveralls & Appveyor](http://david-grs.github.io/cpp-clang-travis-cmake-gtest-coveralls-appveyor/)
- [Boost.Tests](http://www.boost.org/doc/libs/1_63_0/libs/test/doc/html/)
- [Catch](https://github.com/catchorg/Catch2)

```
Copyright (c) 2015-2020 The ISC Authors
```
