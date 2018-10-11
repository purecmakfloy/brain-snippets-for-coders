# Инструкция для сборки приложений на Qt в один exe без зависимостей: MSYS64, MINGW64


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
8)
