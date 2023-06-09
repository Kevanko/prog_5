# prog_5
Методические указания
В рамках лабораторной работы необходимо реализовать обработку строковой информации в соответствии с заданием. В качестве обрабатываемых данных рассматриваются пути к файлам в операционных системах Windows и GNU/Linux.

Путь (англ. path) – набор символов, описывающий расположение файла в файловой системе. В UNIX-подобных операционных системах разделительным знаком при записи пути является символ «/» (слэш), в ОС Windows – символ «" (обратный слэш).

Файловые пути в ОС Windows
Полное имя файла в Windows-системах состоит из буквы диска, после которого ставится двоеточие и обратная наклонная черта (обратный слеш), затем через обратные слеши перечисляются подкаталоги, в конце пишется имя файла. Например:

C:\Windows\System32\calc.exe
Полное имя файла (включая расширение) в ОС Windows может содержать до 260 символов, данное значение определено константой MAX_PATH.

Пути, не имеющие в начале буквы диска и двоеточия, считаются относительными и отсчитываются относительно рабочего каталога. Например, если текущим является каталог C:\Windows, то относительный путь System32\calc.exe соответствует абсолютному пути C:\Windows\System32\calc.exe.

Граф структуры файлов ОС Windows представляет собой лес деревьев, корнями каждого из которых являются дисковые разделы:

../_images/win_paths.png
Для описания пути к файлам, расположенным на удаленных узлах вычислительной сети используется стандарт UNC (Universal Naming Convention). Описание файлового пути в формате UNC имеет следующий формат:

\\<Имя или IP адрес узла>\<Путь к ресурсу на указанном узле>
Например:

\\192.168.1.2\Share\file.txt
\\windows-machine.com\Share\file.txt
IP адрес представляет собой набор из четырех целых чисел, разделенных точкой:

IP=x1.x2.x3.x4
где 0≤xi≤255
, например: 192.168.1.1, 93.158.134.3, 173.194.47.164, 91.196.245.216.

Имя узла представляет собой доменное имя, которое состоит из непосредственного имени узла и далее имён всех доменов, в которые он входит, разделённых точками. Например, полное имя rus.wikipedia.org обозначает узел с именем rus, который входит в домен второго уровня .wikipedia, который входит в домен первого уровня .org.

В рамках данной лабораторной работы будем считать, что существует только три домена верхнего уровня: ru, com и org. Также в имени домена могут присутствовать только латинские буквы.

Примечание

Символ «обратный слэш» – «" является служебным в языке Си и выполняет функции экранирования. С помощью него записываются спецсимволы, например \n, \t и т.п. Для использования данного символа в обычном контексте его также нужно экранировать: \\. Таким образом для того, чтобы в языке Си записать константную строку, содержащую путь C:\Windows\System32\calc.exe, требуется заменить каждый обратный слэш на двойной:

char str[] = "C:\\Windows\\System32\\calc.exe";
Файловые пути в UNIX-подобных ОС
В UNIX-подобных операционных системах (например, GNU/Linux) полный путь состоит из слеша («/»), обозначающего корневой каталог, после которого идет перечисление каталогов и подкаталогов, разделенных также символом «/», в конце указывается имя файла. Например:

/usr/local/bin/gcc
Пути, начинающиеся не с символа «/», считаются относительными и отсчитываются относительно рабочего каталога. Пусть текущий каталог – /home/alex/video, тогда относительный путь prog/c_manual.avi соответствует абсолютному пути /home/alex/video/prog/c_manual.avi.

Особое значение имеют пути, которые начинаются со знака тильда («~»). Тильда обозначает домашнюю директорию текущего или указанного пользователя.

Если после знака тильда («~») следует слэш («/»), то речь идет о пути относительно домашнего каталога текущего пользователя:

Пусть текущий пользователь – iv622s01 Домашний каталог пользователя iv622s01 – /home/STUDENTS/iv421s01/. Тогда путь ~/video/prog/c_manual.avi соответствует абсолютному пути: /home/STUDENTS/iv421s01/video/prog/c_manual.avi.

Если после знака тильда («~») следуют любой другой символ, то подстрока, содержащая символы, расположенные между знаком «~» и первым знаком «/» рассматриваются как имя пользователя в данной ОС, а подстрока, расположенная справа от первого знака «/» трактуется как путь относительно домашнего каталога этого пользователя ОС. При этом текущий пользователь значения не имеет.

Пусть текущий пользователь – iv421s01. Домашние каталоги пользователей находятся в /home/STUDENTS/. Тогда путь ~is441s15/video/prog/c_manual.avi соответствует абсолютному пути: /home/STUDENTS/is441s15/video/prog/c_manual.avi

Граф структуры файлов ОС GNU/Linux представляет собой единое дерево, корнем которого является один из доступных дисковых разделов. Остальные разделы монтируются (присоединяются) к основному дереву ниже первого уровня иерархии:

../_images/linux_paths.png
Для описания пути к файлам, расположенным на удаленных узлах вычислительной сети используется два варианта записи (в зависимости от используемой программы). Формат записи, применяемый в таких программах, как scp, rcp, rsync, выглядит следующим образом:

<Имя или IP адрес узла>:<Путь к ресурсу на указанном узле>
Например:

192.168.1.2:/Share/file.txt
linux-machine.com:/Share/file.txt
Также распространено использование формата URL (Universal Resource Location), синтаксис которого выглядит следующим образом:

<протокол>://<Имя или IP адрес узла>[:<порт>]/<Путь к ресурсу на узле>
Где протокол – строка, порт – целое число в диапазоне [0, 65535] Например:

http://192.168.1.2:8080/Share/file.txt
smb://linux-machine.com/Share/file.txt
smb://ya.ru:80/Share/file.txt
Примечание

В рамках данной лабораторной работы будем рассматривать следующие протоколы: http, ftp, rsync, smb. Использование любых других протоколов недопустимо и считается ошибкой.

Обозначение текущего и родительского каталогов
В операционных системах Windows и GNU/Linux существуют специальные обозначения для текущего каталога (строка «.») и каталога, который является родительским для текущего (строка «..»).

Пусть текущий каталог – /home/alex/video, тогда относительный путь ./prog/c_manual.avi соответствует абсолютному пути /home/alex/video/prog/c_manual.avi, а относительный путь ../projects/lab1/lab1.c соответствует абсолютному пути /home/alex/projects/lab1/lab1.c.

Абсолютному пути /home/alex/video/./prog/c_manual.avi соответствует минимальный путь /home/alex/video/prog/c_manual.avi

Абсолютному пути /home/alex/video/../projects/lab1/lab1.c соответствует минимальный путь: /home/alex/projects/lab1/lab1.c.

Преобразование путей ОС Windows в пути ОС GNU/Linux в эмуляторе Cygwin
Эмулятор Linux-окружения Сygwin (http://www.cygwin.com/ ) предоставляет пользователю возможность работы в Linux-подобной среде из операционной системы Windows. В связи с различиями между графами файловых структур в ОС GNU/Linux и Windows для доступа к файлам ОС Windows из Linux-окружения выполняется преобразование их путей. Преобразование леса деревьев к дереву заключается в подключении (монтировании) его корней к заранее определенному каталогу /cygdrive. Таким образом, путь C:\somedir\somefile транслируется в /cygdrive/c/somedir/somefile.

../_images/cygwin_paths.png
Запрещенные символы
Многие операционные системы запрещают использование некоторых служебных символов. Запрещенные символы Windows:

Символ	Описание
\	разделитель подкаталогов
/	ключи командного интерпретатора
:	отделяет букву диска в Windows-путях
*	заменяющий символ (маска «любое количество любых символов»)
?	заменяющий символ (маска «один любой символ»)
«	используется для указания путей, содержащих пробелы
<	обозначение перенаправления ввода
>	обозначение перенаправления вывода
|	обозначение программного конвейера
В UNIX и UNIX-подобных ОС запрещен слеш («/») – разделитель подкаталогов – и символ конца строки (\0). Остальные перечисленные выше символы (кроме слеша) использовать можно, но из соображений совместимости их лучше избегать.

Примечание

В рамках данной лабораторной работы выдвигается требование совместимости файловых путей в ОС Windows и GNU/Linux. Поэтому использование запрещенных символов не допускается ни в Windows-путях ни в Linux-путях. Длина пути также не должна превышать ограничение, установленное в ОС Windows (MAX_PATH = 260 символов).

Задание
Общая информация
Номер варианта выбирается в соответствии с номером студента в журнале по формуле (i–1)%n+1
, где i
 – номер студента по журналу, n
 – количество заданий.

При разработке подпрограмм в соответствии с заданием следует учитывать, что:

Исходная задача должна быть разбита на четыре основные подзадачи:

ввод данных – функция input() – предусматривает взаимодействие с пользователем и возвращает строку, содержащую входные данные (путь);
проверка корректности данных – check() – которая обеспечивает проверку допустимости длины входной строки и используемых в ней символов. Значения, возвращаемые функцией check(), должны позволять определить тип ошибки и (если возможно) номер символа, в которой она обнаружена;
обработка – process() – входных данных согласно заданию;
вывод данных – output() – на экран, обеспечивает отображение полученных результатов или сообщений об ошибке.
Взаимодействие между указанными функциями осуществляется через данные. Каждая из них, при необходимости, также разбивается на подзадачи. Разбиение производится до тех пор, пока подзадачи не становятся тривиальными, например, вычисление длины строки.

Каждая подзадача оформляется в виде функции.

Не допускается использования стандартных функций обработки строк. Все операции над строками должны быть реализованы самостоятельно в виде отдельных подпрограмм. Эти подпрограммы необходимо разместить в отдельном файле strings.c, а прототипы функций – в файле strings.h.

Примерами типичных функций, которые должны присутствовать в каждой программе являются:

функция вычисления длины строки (slen);
функция разбиения строки на элементы-токены (stok), разделенные заданным символом (например, символ “/” при анализе пути или символ “.”, при разборе IP-адресов или доменных имен);
функция проверки символа на принадлежность заданному множеству символов (sspn), необходимая для проверки допустимости используемых символов.
функция сравнения строк (scmp);
функция копирования строк (scpy).
Набор тестов должен обеспечивать проверку поведения программы для всех классов входных данных:

некорректный файловый путь;
превышение допустимой длины пути;
допустимый путь, который не удовлетворяет указанным в задании условиям;
допустимый путь, удовлетворяющий условиям.
Для определения типа ОС для абсолютного пути необходимо проверить следующие характеристики:

ОС	Характеристики пути
Linux	Путь не содержит ни одного Windows-разделителя (“').
Windows	
Путь не содержит ни одного Linux-разделителя („/“).
В начале пути располагается имя раздела, которое может состоять из одной („A“, „C“, „d“) или двух („AA“, „Bd“, „zz“) букв латинского алфавита в произвольном регистре.
Имя раздела отделяется от списка директорий при помощи двоеточия, после которого следует знак «".
Требования к IP-адресам и доменным именам:

Адресация узла	 
IP-адрес	
Строка, содержащая четыре десятичных числа, разделенных точками, каждое из которых не превышает 255. Например:

Корректные значения: «10.125.200.4», «192.168.0.1»

Некорректные значения: «10.125.200», «400.168.0.1», «x.125.200.4»

Доменное имя	
Доменное имя содержит набор имен доменов, разделенных точками. В рамках данной лабораторной работы действуют следующие ограничения:

Имя домена состоит только из латинских символов.

Доменами верхнего уровня (самый правый) может быть только ru, com, org.

глубина вложенности не должна быть больше четырех.

Корректные значения: «my.ya.ru», «csc.sibsutis.ru»

Некорректные значения: «10.ya.ru», «my.ya.eu»,
«a.b.c.d.e» (глубина вложенности – 5).

Задание для факультета ИВТ
Входными данными для всех подпрограмм является строка, содержащая пути к файлам, разделенные указанным видом разделителей (может быть пробел, двоеточие, «+»). На вход могут также поступать дополнительные данные, определяемые вариантом задания.

Вариант 1
Файлы, расположенные в каталоге dir1 были перемещены в каталог dir2. Для заданных с клавиатуры dir1 и dir2 обновить список входных файловых путей. dir1 и dir2 должны относиться к одной и той же ОС.

Вход:

delim: +
paths: C:\Windows\system32+C:\User\test+C:\Windows\explorer.exe+D:\Windows\Distrib
dir1: C:\Windows
dir2: E:\WindowsXP
Выход:

new paths: E:\WindowsXP\system32+C:\User\test+E:\WindowsXP\explorer.exe+D:\Windows\Distrib
Вариант 2
Для заданного имени текущего пользователя обновить список входных файлов, преобразовав пути, заданные относительно его домашнего каталога (вида ~/somedir), к абсолютным путям. Имя каталога (dir), в котором находятся домашние каталоги пользователей, вводится с клавиатуры.

Вход:

delim: +
user name: jack
dir: /home/stud
paths: ~/games/packman.cpp+~alex/docs+~/study/Prog/lab4.c+/usr/bin/gcc
Выход:

new paths: /home/stud/games/packman.cpp+~alex/docs+/home/stud/study/Prog/lab4.c +/usr/bin/gcc
Вариант 3
Преобразовать все пути входного списка, заданные относительно домашних каталогов указанных пользователей (вида ~alex/somedir), к абсолютным путям. Имя каталога (dir), в котором находятся домашние каталоги пользователей, вводится с клавиатуры.

Вход:

delim: +
dir: /home/stud
paths: ~/games/packman.cpp+~alex/docs+~/study/Prog/lab4.c+/usr/bin/gcc
Выход:

new paths: ~/games/packman.cpp+/home/stud/alex/docs+~/study/Prog/lab4.c +/usr/bin/gcc
Вариант 4
Преобразовать все пути, содержащие служебные каталоги: текущий каталог («.») и родительский каталог («..»); к минимально возможным путям.

Вход:

delim: +
paths: /home/alex/video/../progs/./test.c+~alex/docs+~/study/Prog/lab4.c+/usr/bin/gcc
Выход:

new paths: /home/alex/progs/test.c+~alex/docs+~/study/Prog/lab4.c+/usr/bin/gcc
Вариант 5
Преобразовать все Windows пути во входном списке к Linux-путям, используя формат эмулятора Cygwin.

Вход:

delim: +
paths: C:\Windows\system32+E:\Distrib\msoffice.exe+/home/alex/prog/lab4.c
Выход:

new paths: /cygdrive/c/Windows/system32+/cygdrive/e/Distrib/msoffice.exe+/home/alex/prog/lab4.c
Вариант 6
Преобразовать все Windows-пути формата Cygwin к оригинальным Windows-путям.

Вход:

delim: +
paths: cygdrive/c/Windows/system32+/cygdrive/e/Distrib/msoffice.exe+/home/alex/prog/lab4.c
Выход:

new paths: C:\Windows\system32+E:\Distrib\msoffice.exe+/home/alex/prog/lab4.c
Вариант 7
На сетевых узлах была выполнена замена ОС Windows на ОС GNU/Linux. Сетевые ресурсы (файлы) были перенесены без изменения иерархии. В ОС GNU/Linux доступ к файлам осуществляется с помощью программы scp. Обновить все сетевые пути входного списка. Необходимо проверять допустимость всех IP-адресов, доменных имен и протоколов в списке путей. Некорректные пути исключать из списка.

Вход:

delim: +
paths: \\192.168.1.1\test+\\mysrv.com\Windows\system32+\\192.500.1.1\test+\\my.fr\m\n\k.txt
Выход:

new paths: 192.168.1.1:/test+mysrv.com:/Windows/system32
Вариант 8
На сетевых узлах была выполнена замена ОС Windows на ОС GNU/Linux. Файлы были перенесены без изменения иерархии. В ОС GNU/Linux доступ к файлам будет осуществляться по протоколу http (формат пути – URL). Обновить все сетевые пути входного списка. Необходимо проверять допустимость всех IP-адресов, доменных имен и протоколов в списке путей.

Вход:

delim: +
paths: \\192.168.1.1\test+\\mysrv.com\Windows\system32+\\192.500.1.1\test+\\my.fr\m\n\k.txt
Выход:

new paths: http://192.168.1.1/test+http://mysrv.com/Windows/system32
Вариант 9
Файлы из входного списка, расположенные на сетевом узле, работающем под управлением ОС GNU/Linux, были перенесены на новый узел под управлением ОС Windows. Иерархия каталогов не изменялась. Доступ к файлам осуществлялся по протоколам smb и http (формат пути – URL). По заданным именам или IP адресам исходного и нового узлов обновить входной список файлов. Необходимо проверять допустимость всех IP-адресов, доменных имен и протоколов в списке путей.

Вход:

delim: +
paths: smb://192.168.1.1/test+http://mysrv.com/Windows/+http://192.500.1.1/test+ftp://my.ru/m/n/k.txt
Выход:

new paths: \\192.168.1.1\test+\\mysrv.com\Windows\
Вариант 10
Файлы из входного списка, расположенные на сетевом узле, работающем под управлением ОС GNU/Linux, были перенесены на новый узел под управлением ОС Windows. Иерархия каталогов не изменялась. Доступ к файлам осуществлялся с помощью программы scp. По заданным именам или IP адресам исходного и нового узлов обновить входной список файлов. Необходимо проверять допустимость всех IP-адресов, доменных имен и протоколов в списке путей.

Вход:

delim: +
paths: 192.168.1.1:/test+mysrv.com:/Windows/+ 192.500.1.1:/test+my.fr:/m/n/k.txt
Выход:

new paths: \\192.168.1.1\test+\\mysrv.com\Windows\
Задание для факультета МРМ
Входными данными для всех подпрограмм является путь к одному файлу.

Вариант 1
На вход программы поступает строка, соответствующая абсолютному пути. Для заданной строки выполнить следующие операции:

определить тип ОС, использующей данный путь;
найти глубину (depth) вложенности конечного файла в структуру каталогов (количество каталогов, начиная с корневого, которое необходимо пройти, чтобы получить доступ к файлу);
вывести имя каталога(каталогов), имеющее наибольшую длину.
Вход:

/home/qwert/sandbox/openmpi/1.6.5/lib/OpenMPI/mca_btl_tcp.so
Выход:

OS: Linux
Depth: 8
Max dir name: sandbox, openmpi, OpenMPI
Вход:

CA:\home\qwert\sandbox\openmpi\1.6.5\lib\OpenMPI\mca_btl_tcp.so
Выход:

OS: Windows
Depth: 8
Max dir name: sandbox, openmpi, OpenMPI
Замечание: для решения задачи пункта (3) используйте дополнительный массив для хранения индексов каталогов, длина которых соответствует текущему максимуму. При обнаружении нового максимума ранее сохраненные индексы должны отбрасываться.

Вариант 2
На вход программы поступает строка, соответствующая абсолютному пути. Для заданной строки выполнить следующие операции:

определить тип ОС, использующей данный путь;
найти количество символов в имени конечного файла (длина файла без учета каталогов) и распечатать его имя;
определить, присутствуют ли во входном пути директории, имена которых состоят только из цифр, если да – заменить имя такого каталога на число, которое на единицу превышает числовое представление имени каталога.
Вход:

/home/qwert/9/openmpi/165/lib/OpenMPI/mca_btl_tcp.so
Выход:

OS: Linux
file name: mca_btl_tcp.so
file name length: 14
updated path: /home/qwert/10/openmpi/166/lib/OpenMPI/mca_btl_tcp.so
Вход:

CA:\home\qwert\9\openmpi\165\lib\OpenMPI\mca_btl_tcp.so
Выход:

OS: Windows
file name: mca_btl_tcp.so
file name length: 14
updated path: CA:\home\qwert\10\openmpi\166\lib\OpenMPI\mca_btl_tcp.so
Вариант 3
На вход программы поступает строка, соответствующая абсолютному пути. Для заданной строки выполнить следующие операции:

определить тип ОС, использующей данный путь;
преобразовать все символы в имени конечного файла (без учета каталогов) к верхнему регистру;
расположить имена каталогов в пути в обратном порядке.
Вход:

/home/openmpi/1.6.5/lib/OpenMPI/mca_btl_tcp.so
Выход:

OS: Linux
updated path: /OpenMPI/lib/1.6.5/openmpi/home/MCA_BTL_TCP.SO
Вход:

CA:\home\openmpi\1.6.5\lib\OpenMPI\mca_btl_tcp.so
Выход:

OS: Windows
updated path: CA:\OpenMPI\lib\1.6.5\openmpi\home\MCA_BTL_TCP.SO
Вариант 4
На вход программы поступает строка, соответствующая абсолютному пути и произвольная последовательность символов. Для заданной строки выполнить следующие операции:

определить тип ОС, использующей данный путь;
преобразовать все символы в имени каталога, содержащего конечный файл, к нижнему регистру;
исключить из пути (после преобразования (б)) все директории, в имя которых входит заданная последовательность символов.
Вход:

path: /home/testmpi/1.6.5/lib/OpenMPI/mca_btl_tcp.so
sequence: mpi
Выход:

OS: Linux
Update#1: /home/testmpi/1.6.5/lib/openmpi/mca_btl_tcp.so
Update#2: /home/1.6.5/lib/mca_btl_tcp.so
Вход:

path: CA:\home\testmpi\1.6.5\lib\OpenMPI\mca_btl_tcp.so
sequence: mpi
Выход:

OS: Windows
Update#1: CA:\home\testmpi\1.6.5\lib\openmpi\mca_btl_tcp.so
Update#2: CA:\home\1.6.5\lib\mca_btl_tcp.so
Вариант 5
На вход программы поступает строка. Для заданной строки выполнить следующие операции:

определить, соответствует ли заданная строка сетевому пути формата UNC (используемому в ОС Windows), в котором для указания имени узла используется IP-адрес. Необходимо также проверить допустимость IP-адреса.
если расширение файла «.exe» заменить его на «.bin».
Вход:

path: \\192.168.10.2\home\openmpi\mpicc.exe
Выход:

Is UNC: yes
IP is correct: yes
Updated path: \\192.168.10.2\home\openmpi\mpicc.bin
Вход:

path: \\192.168.10.2\home\openmpi\mpicc.txt
Выход:

Is UNC: yes
IP is correct: yes
Updated path: \\192.168.10.2\home\openmpi\mpicc.txt
Вход:

path: \\192.168.10.300\home\openmpi\mpicc.exe
Выход:

Is UNC: yes
IP is correct: no
Вход:

path: 192.168.10.2:/home/openmpi/mpicc.exe
Выход:

Is UNC: no
Вариант 6
На вход программы поступает строка. Для заданной строки выполнить следующие операции:

определить, соответствует ли заданная строка сетевому пути формата UNC (используемому в ОС Windows), в котором для указания имени узла используется доменное имя. Необходимо также проверить допустимость доменного имени.
если расширение файла «.exe» заменить его на «.bin».
Вход:

path: \\my.test.ru\home\openmpi\mpicc.exe
Выход:

Is UNC: yes
Domain name is correct: yes
Updated path: \\ my.test.ru\home\openmpi\mpicc.bin
Вход:

path: \\ my.test.ru\home\openmpi\mpicc.txt
Выход:

Is UNC: yes
Domain name is correct: yes
Updated path: \\my.test.ru\home\openmpi\mpicc.txt
Вход:

path: \\a.b.c.d.e\home\openmpi\mpicc.exe
Выход:

Is UNC: yes
Domain name is correct: no
Вход:

path: \\a.b.c.eu\home\openmpi\mpicc.exe
Выход:

Is UNC: yes
Domain name is correct: no
Вариант 7
На вход программы поступает строка. Для заданной строки выполнить следующие операции:

определить, соответствует ли заданная строка сетевому пути, используемому в утилитах SCP, RSYNC и т.д. (ОС Linux), в котором для указания имени узла используется IP-адрес. Необходимо также проверить допустимость IP-адреса.
привести все имена каталогов и файлов в пути к нижнему регистру.
Вход:

path: 192.168.10.2:home/OpenMPI/MPicc.exe
Выход:

Is SCP: yes
IP is correct: yes
Updated path: 192.168.10.2:home/openmpi/mpicc.exe
Вход:

path: 192.168.10.300:home/OpenMPI/MPicc.exe
Выход:

Is SCP: yes
IP is correct: no
Вход:

path: \\192.168.10.2\home\openmpi\mpicc.exe
Выход:

Is SCP: no
Вариант 8
На вход программы поступает строка. Для заданной строки выполнить следующие операции:

определить, соответствует ли заданная строка сетевому пути, используемому в утилитах SCP, RSYNC и т.д. (ОС Linux), в котором для указания имени узла используется доменное имя. Необходимо также проверить допустимость доменного имени.
привести все имена каталогов и файлов в пути к нижнему регистру.
Вход:

path: my.test.ru:home/OpenMPI/MPicc.exe
Выход:

Is SCP: yes
Domain name is correct: yes
Updated path: my.test.ru:home/openmpi/mpicc.exe
Вход:

path: a.b.c.d.e:home/OpenMPI/MPicc.exe
Выход:

Is SCP: yes
Domain name is correct: no
Вход:

path: 192.168.2.2:home/OpenMPI/MPicc.exe
Выход:

Is SCP: no
Вариант 9
На вход программы поступает строка, содержащая файловый путь, а также два символа. Выполнить следующие операции:

определить, соответствует ли заданная строка сетевому пути формата URL (ОС Linux), в котором для указания имени узла используется IP-адрес. Необходимо также проверить допустимость IP-адреса.
Заменить все вхождения первого символа в именах каталогов на второй.
Вход:

path: http://192.168.10.2/home/Open-MPI/MPicc-test.exe
symbol1: -
symbol2: _
Выход:

Is URL: yes
IP is correct: yes
Updated path: http://192.168.10.2/home/Open_MPI/MPicc_test.exe
Вход:

path: http://192.168.10.300/home/Open-MPI/MPicc-test.exe
Выход:

Is URL: yes
IP is correct: no
Вход:

path: \\192.168.10.2\home\openmpi\mpicc.exe
Выход:

Is URL: no
Вариант 10
На вход программы поступает строка, содержащая файловый путь, а также два символа. Выполнить следующие операции:

определить, соответствует ли заданная строка сетевому пути формата URL (ОС Linux), в котором для указания имени узла используется IP-адрес. Необходимо также проверить допустимость IP-адреса.
Заменить все вхождения первого символа в именах каталогов на второй.
Вход:

path: http://my.ya.ru/home/Open-MPI/MPicc-test.exe
symbol1: -
symbol2: _
Выход:

Is URL: yes
Domain name is correct: yes
Updated path: http://my.ya.ru/home/Open_MPI/MPicc_test.exe
Вход:

path: http://10.ya.ru/home/Open-MPI/MPicc-test.exe
Выход:

Is URL: yes
Domain name is correct: no
Вход:

path: \\192.168.10.2\home\openmpi\mpicc.exe
Выход:

Is URL: no
