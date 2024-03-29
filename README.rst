.. role:: shell(code)
   :language: shell

.. image:: https://github.com/LushinEA/rest-api-service/workflows/CI/badge.svg?branch=master&event=push
    :target: https://github.com/LushinEA/rest-api-service/actions?query=workflow%3ACI

Что внутри?
===========
Приложение упаковано в Docker-контейнер и разворачивается с помощью Ansible.

Внутри Docker-контейнера доступны две команды: :shell:`analyzer-db` — утилита
для управления состоянием базы данных и :shell:`analyzer-api` — утилита для 
запуска REST API сервиса.

Как использовать?
=================
Как применить миграции:

.. code-block:: shell

    docker run -it \
        -e ANALYZER_PG_URL=postgresql://user:hackme@localhost/analyzer \
        LushinEA/rest-api-service analyzer-db upgrade head

Как запустить REST API сервис локально на порту 8081:

.. code-block:: shell

    docker run -it -p 8081:8081 \
        -e ANALYZER_PG_URL=postgresql://user:hackme@localhost/analyzer \
        LushinEA/rest-api-service

Все доступные опции запуска любой команды можно получить с помощью
аргумента :shell:`--help`:

.. code-block:: shell

    docker run LushinEA/rest-api-service analyzer-db --help
    docker run LushinEA/rest-api-service analyzer-api --help

Опции для запуска можно указывать как аргументами командной строки, так и
переменными окружения с префиксом :shell:`ANALYZER` (например: вместо аргумента
:shell:`--pg-url` можно воспользоваться :shell:`ANALYZER_PG_URL`).

Как развернуть?
---------------
Чтобы развернуть и запустить сервис на серверах, добавьте список серверов в файл
deploy/hosts.ini (с установленной Ubuntu) и выполните команды:

.. code-block:: shell

    cd deploy
    ansible-playbook -i hosts.ini --user=root deploy.yml

Разработка
==========

Быстрые команды
---------------
* :shell:`make` Отобразить список доступных команд
* :shell:`make devenv` Создать и настроить виртуальное окружение для разработки
* :shell:`make postgres` Поднять Docker-контейнер с PostgreSQL
* :shell:`make lint` Проверить синтаксис и стиль кода с помощью `pylama`_
* :shell:`make clean` Удалить файлы, созданные модулем `distutils`_
* :shell:`make test` Запустить тесты
* :shell:`make sdist` Создать `source distribution`_
* :shell:`make docker` Собрать Docker-образ
* :shell:`make upload` Загрузить Docker-образ на hub.docker.com

.. _pylama: https://github.com/klen/pylama
.. _distutils: https://docs.python.org/3/library/distutils.html
.. _source distribution: https://packaging.python.org/glossary/

Как подготовить окружение для разработки?
-----------------------------------------
.. code-block:: shell

    make devenv
    make postgres
    source env/bin/activate
    analyzer-db upgrade head
    analyzer-api

После запуска команд приложение начнет слушать запросы на 0.0.0.0:8081.
Для отладки в PyCharm необходимо запустить :shell:`env/bin/analyzer-api`.

Как запустить тесты локально?
-----------------------------
.. code-block:: shell

    make devenv
    make postgres
    source env/bin/activate
    pytest

Для отладки в PyCharm необходимо запустить :shell:`env/bin/pytest`.

Как запустить нагрузочное тестирование?
---------------------------------------
Для запуска `locust`_ необходимо выполнить следующие команды:

.. code-block:: shell

    make devenv
    source env/bin/activate
    locust

После этого станет доступен веб-интерфейс по адресу http://localhost:8089

.. _locust: https://locust.io
