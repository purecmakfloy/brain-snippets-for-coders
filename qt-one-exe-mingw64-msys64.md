# Инструкция для сборки приложений на Qt в один exe без зависимостей: MSYS64, MINGW64

![Иллюстрация к проекту](https://raw.githubusercontent.com/karushifa/snippets-code/master/index.png)

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

5) Устанавливаем MINGW64 из консоли MSYS64: 
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
./configure.exe -static -prefix "C:/Qt/Qt5.2.0_static" -opengl desktop -nomake examples -nomake tests 
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
cd C:/openssl-1.0.0c
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

![Иллюстрация к проекту](https://raw.githubusercontent.com/karushifa/snippets-code/master/index2.png)

16) Снова:
```
make
```

16) Повторная ошибка missing argument с вычеслением ее устранения в скриншотах:
![Иллюстрация к проекту](https://raw.githubusercontent.com/karushifa/snippets-code/master/index3.png)
![Иллюстрация к проекту](https://raw.githubusercontent.com/karushifa/snippets-code/master/index4.png)
![Иллюстрация к проекту](https://raw.githubusercontent.com/karushifa/snippets-code/master/index5.png)
Переходим в нужную папку и в файле Makefile.Release исправляем строку с "-L"...

17) Снова и так каждый раз:
```
make
```

# Я специально указываю свои ошибки во время компиляции и способы их устранения

18) Когда так спустя несколько часов, значит ты Герой:

![Иллюстрация к проекту](https://raw.githubusercontent.com/karushifa/snippets-code/master/index6.png)

19) Пишем: 
```
make install
```
20) Далее открываем Qtcreator, настраиваем Комплекты, Профили, Компиляторы и Отдачики под наши нужны указывая Mingw64 который лежит в папке с MSYS и создаем проект. 

- ожидаем окончания копирования

- запускаем QtCreator

- в меню нажимаем "Инструменты" -> нажимаем "Параметры"

- слева выбираем "Сборка и запуск", а справа выбираем вкладку "Профили Qt"

- нажимаем кнопку "Добавить" и указываем путь до qmake.exe "C:\Qt\Qt5.2.0_static\bin\qmake.exe "

- нажимаем кнопку "Применить"

- переходим на вкладку "Комплекты" и нажимаем кнопку "Добавить"

- в поле "Название" вводим имя Desktop Qt 5.2.0 Static MinGW 32 bit

- если поле "Отладчик" пусто, то нажимаем кнопку "Управление"

- нажимаем кнопку "Добавить", вводим название (например gdb) и указываем путь к отладчику "C:\Qt\Qt5.2.0\Tools\mingw48_32\bit \gdb.exe"

- нажимаем кнопку "Применить"

- возвращаемся на на вкладку "Комплекты"

- в выпадающем списке "Профиль Qt" выбираем тот, которые мы указали на вкладке "Профили Qt"

- выделяем комплект "Desktop Qt 5.2.0 Static MinGW 32 bit" и нажимаем кнопку "Сделать по умолчанию" (если хотим чтобы комплект для статической сборки был поумолчанию)

- нажимаем кнопку "OK"

- протестируем, что приложения собираются статически

- создадим проект, для этого в меню нажимаем "Файл" -> нажимаем "Новый файл или проект..."

- слева в списке "Проекты" выбираем "Приложения" -> справа выбираем "Приложения Qt Widgets"

- в поле "Название" вводим: Hello_Static_Qt

- в поле "Создать" в вводим путь к папке с проектами, например: I:\QtProjects

- нажимаем кнопку "Далее"

- ставим галочки (по умолчанию должно быть выбрано) на два комплекта:

Desktop Qt 5.2.0 MinGW 32 bit
Desktop Qt 5.2.0 Static MinGW 32 bit

- нажимаем кнопку "Далее" -> нажимаем кнопку "Завершить"

- на панели слева нажимаем "Проекты"

- в верхней части экрана нажимаем "Desktop Qt 5.2.0 Static MinGW 32 bit"

- в разделе "Настройки сборки" в выпадающем списке "Изменить конфигурацию сборки" выбираем "Выпуск"

- находим раздел "Этапы сборки"

- напротив "qmake" выбираем "Подробнее"

- в выпадающем списке "Конфигурация сборки qmake" выбираем "Выпуск"

- в поле "Дополнительные параметры" вводим: "CONFIG += release" (с двойными кавычками)

- на панели слева нажимаем "Редактор"

- в меню нажимаем "Сборка" и нажимаем "Собрать проект "Hello_Static_Qt""

- переходим в папку с exe-файлом и запускаем его: I:\QtProjects\build-Hello_Static_Qt-Desktop_Qt_5_2_0_Static_MinGW_32bit-Выпуск\release 

#PS:

Компилируем его под "Профилирование" - В итоге получаем действительно работающий билд.
![Иллюстрация к проекту](https://raw.githubusercontent.com/karushifa/snippets-code/master/index7.png)
![Иллюстрация к проекту](https://raw.githubusercontent.com/karushifa/snippets-code/master/index8.png)

#Ужасссс
