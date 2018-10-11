# Инструкция для сборки приложений на Qt в один exe без зависимостей: MSYS64, MINGW64

![Иллюстрация к проекту]()

ВНИМАНИЕ!: ДАННЫЙ ГАЙД МОЖЕТ ОТНЯТЬ СУТКИ ЖИЗНИ И У ВАС МОЖЕТ НИЧЕГО НЕ ПОЛУЧИТСЯ


Основано на статье: http://www.programmersforum.ru/showthread.php?t=253646


Основано на статье: https://wiki.qt.io/Compiling_OpenSSL_with_MinGW


Основано на статье: https://www.reddit.com/r/C_Programming/comments/8muc22/mingww64_qtcreator_and_gdb_installation_with/


Версия Qt: Qt 5.2.0 for Windows 32-bit


Версия MSYS: крайняя MSYS2 64-bit


Версия MINGW: крайняя из MSYS2 - MINGW 64-bit


Версия OPENSSL: openssl-1.0.1c


1) Cкачиваем и устанавливаем Qt 5.2.0 for Windows 32-bit (MinGW 4.8, OpenGL, 689 MB) (при установке ставим галочку Source Components) http://qt-project.org/downloads

2) Переходим в каталог C:\Qt\5.2.0\Src\qtbase\mkspecs\win32-g++ и открываем файл "qmake.conf"

3) В файле "qmake.conf" отыскиваем переменную QMAKE_LFLAGS и приравниваем ей значение, вот так:
```
  #QMAKE_LFLAGS = -static -static-libgcc 
```
4) Теперь установим MSYS64. Скачиваем его, устанавливаем. https://www.msys2.org/

5) Устанавливаем MINGW64: 
```
pacman -S mingw-w64-x86_64-gdb mingw-w64-x86_64
```
```
pacman -S --needed base-devel mingw-w64-x86_64-toolchain git subversion mercurial mingw-w64-x86_64-cmake
```
```
pacman -Syyuu
```
6) Переходим в папку с сорцами 
```
cd C:/Qt/5.2.0/Src/qtbase/
```
7) Конфигугируем так в MSYS:
```
./configure.exe -static -prefix "C:\Qt\Qt5.2.0_static" -opengl desktop -nomake examples -nomake tests 
```
8) После конфигурации - самое долгое и ужасное сборочка:
```
make
```
9) Ошибка номер 1: Проблема с исходником OPENSSL. Скачиваем нужную нам версию https://www.openssl.org/source/old/1.0.0/openssl-1.0.0c.tar.gz
10) Распаковываем командой в констоли MSYS:
```
tar xvzf openssl-1.0.1c.tar.gz
```
11) Распокавали? Теперь руками переносим папку на диск Це. C:\openssl-1.0.0c

12) Теперь собираем этот доп: В MSYS консоли пишем
```
export PATH="/c/msys64/mingw64/bin:$PATH"
./Configure --prefix=$PWD/dist no-idea no-mdc2 no-rc5 shared mingw64
make depend && make && make install
```
13) После компиляции, папку C:\openssl-1.0.0c\include\openssl ташим в C:\msys64\mingw64\include в которой, меняем название оргинальной папки openssl на openssl_bak и копируем нашу приготовленую папку

14) Снова переходим в MSYS консоль и снова
```
make
```
15) Ошибка 2: g++: error: missing argument to ‘-L’, редактируем файл C:\Qt\5.2.0\Src\qtbase\src\tools\uic\Makefile.Release, заменяя ошибочную строку 20 на:
```
LIBS = -LC:\openssl-1.0.0c\dist\lib -LC:/Qt/5.2.0/Src/qtbase/lib -lQt5Core -lole32 -luuid -lws2_32 -ladvapi32 -lshell32 -luser32 -lkernel32 -lz 
```
16) Снова:
```
make
```
