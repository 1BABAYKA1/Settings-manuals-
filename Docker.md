Установка и настройка Docker
Процесс установки Docker очень прост – достаточно воспользоваться менеджером пакетов:

# yum install docker
Какой-либо сложной настройки на данном этапе не требуется. После установки запустите сервис, проверьте его состояние и установите запуск при загрузке:

# systemctl start docker
# systemctl enable docker
# systemctl status docker


Далее запустите тестовый образ, чтобы проверить, что Docker работает правильно:

# docker run hello-world

Вы должны увидеть следующее сообщение:
«Hello from Docker. This message shows that your installation appears to be working correctly.»



Базовые команды Docker
Синтаксис команды docker в общем случае следующий:

docker [ОПЦИИ] КОМАНДА
Если запустить команду без опций, будет выведен список всех доступных команд:

# docker


Опции команды

—config ПУТЬ задать месторасположение файлов конфигурации клиента
-D, —debug режим отладки
-H, —host list сокет демона для подключения
-l, —log-level string уровень ведения лога («debug»-отладка|»info»-информационное сообщение|»warn»-предупреждение|»error»-ошибка|»fatal»-критическая ошибка) (по умолчанию «info»)
—tls использовать TLS; подразумевается при использовании —tlsverify
—tlscacert ЦЕНТР доверять только сертификатам, подписанным заданным центром сертификации
—tlscert string путь к файлу сертификата TLS
—tlskey string путь к файлу ключа TLS
—tlsverify использовать TLS и проверить сертификат у авторизованного центра
-v, —version вывести информацию о версии и выйти

Команды управления:

builder управление сборкой
config управление конфигурацией
container управление контейнерами
engine управление движком
image управление образами
network управление сетью
node управление узлами в режиме «роя» (Swarm – инструмент кластеризации, позволяющий объединить несколько хостов в единый виртуальный хост)
plugin управление дополнениями
secret управление защищенными данными
service управление службами
stack управление стеками образов
swarm управление режимом «роя»
system управление системой
trust управление доверием (подпись образов, отзыв подписи, определение разрешений на подпись и т.д.)
volume управление томами (подключаемыми к контейнеру элементами файловой системы)


 
Системная информация о Docker:

# docker info
Получение помощи:

# docker --help
Команда —help также может использоваться для получения справки по конкретной команде, например:

# docker builder --help
Загрузка образов
Чтобы начать работу с Docker, требуется загрузить на свою машину образ с Docker Hub. Docker Hub представляет собой набор репозиториев, где хранится огромное количество образов различных приложений и систем. Для пользования этими репозиториями потребуется зарегистрировать на Docker Hub свой аккаунт (это бесплатно), а затем войти в него следующей командой:

# docker login
У вас будут запрошены имя пользователя и пароль, введите их для получения доступа.

Теперь мы можем приступить к работе. Допустим, нам требуется образ Ubuntu. Сначала выполним поиск при помощи команды search:

# docker search ubuntu


Выбрав нужный образ, загрузите его на свою машину при помощи команды pull. В данном примере будет загружен официальный образ Ubuntu:

# docker pull ubuntu


Для просмотра всех загруженных в системе образов воспользуйтесь командой images:

# docker images


Если образ больше не нужен, командой rmi его можно удалить:

# docker rmi ubuntu


Запуск образа
Фактически при выполнении над образом какой-либо команды создается контейнер. После завершения ее выполнения контейнер останавливается и остается в незапущенном виде. Если запустить в том же образе другую команду, будет создан новый контейнер, и так далее. Все контейнеры будут оставаться в файловой системе узла, пока вы не удалите их командой rm.

Чтобы создать и запустить контейнер, вам потребуется запустить команду в загруженном образе, в данном случае Ubuntu. Для примера рассмотрим простейшую команду cat, которая выводит файл о версии дистрибутива:

# docker run ubuntu cat /etc/issue
Ubuntu 18.04.2 LTS \n \l
Структура этой команды следующая:

docker run [локальный образ] [запускаемая в контейнере команда]