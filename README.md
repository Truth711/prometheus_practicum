# Prometheus Practicum

## Проверка работоспособности:

Тестирование проводилось на:

 - OS: Windows 10 x64 Pro
 - Окружение: 
	 - Vagrant 2.4.0
	 - VirtualBox 6.1.48

## Запуск:

 1. Склонируйте репозиторий: ` git clone https://github.com/Truth711/prometheus_practicum.git`
 2. Перейдите в папку репозитория: `cd ./prometheus_practicum`
 3. Запустите виртуальные машины:`vagrant up`
 4. Подключаемся на контрольную машину командой: `vagrant ssh controlnode`
 5. * Для Windows! Копируем папку ansible смонтированную от windows и именуем в internal_ansible `cp -R ansible internal_ansible` и даём права 775 на всю папку, иначе не будет работать файл ansible.cfg `chmod -R 775 internal_ansible`
 6. Переходим в папку ansible (или internal_ansible для windows): `cd internal_ansible/`
 7. Запускаем наш плейбук: `ansible-playbook playbook.yml"`

## Практикум по курсу Kubernetes: мониторинг и логирование:
Цель: Закрепление материала полученного в пройденных курсах, знакомство с установкой и использование систем мониторинга и логирования за пределами Kubernetes, получение практического опыта работы с конфигурацией Prometheus и Fluentbit.

Результат: Набор Ansible ролей для установки и конфигурации Prometheus и его экспортеров, а так же стека логирования EFK.
Репозиторий на Github.com с ролями и плейбуками Ansible.

Задание: Необходимо подготовить Ansible роли для разворачивания Prometheus (вне кластера Kubernetes, для мониторинга внешних хостов), для установки Node exporter и добавления static config в конфигурацию развернутого Prometheus с новым хостом, разворачивания ElasticSearch + Kibana, установки fluentbit с настроенным сбором системных логов (из journald) в установленный ElasticSearch.

Всего 4 роли:

Роль для разворачивания Prometheus
Установка Prometheus
Добавление systemd service файла для его запуска
Создание нужных пользователей и директорий с нужными правами и владельцами
Добавление конфигурации с прописанным таргетом для мониторинга самого Prometheus (см. https://prometheus.io/docs/prometheus/latest/getting_started/#configuring-prometheus-to-monitor-itself)
Запуск и релоад сервиса при изменении глобальных параметров конфигурации (не забудьте вынести эти параметры в defaults)
Роль для разворачивания Node Exporter
Установка Node Exporter
Добавление systemd service файла для его запуска
Создание нужных пользователей и директорий с нужными правами и владельцами
Добавление в конфигурацию Prometheus нового хоста через https://prometheus.io/docs/prometheus/latest/configuration/configuration/#static_config (не забудьте что сервер с Prometheusом может находиться не локально, вам нужно уметь добавлять конфигурацию и на удаленный сервер). Учтите, что у вас не должно возникать конфликтов в конфигурации при запуске роли Node Exporter и роли Prometheus, несмотря на то, что обе роли вносят изменения в конфиг.
Запуск сервиса Node Exporter и релоад сервиса Prometheus при добавлении нового хоста в конфигурацию
Роль для разворачивания ElasticSearch и Kibana
Установка ElasticSearch
Установка Kibana
Добавление systemd service файла для запуска обоих
Создание нужных пользователей и директорий с нужными правами и владельцами
Запуск сервисов
Роль для разворачивания FluentBit
Установка FluentBit
Добавление systemd service файла для его запуска
Создание нужных пользователей и директорий с нужными правами и владельцами
Добавление конфигурации со сбором логов из journald и пересылкой их в установленный ElasticSearch.
Запуск сервиса

### Практические рекомендации:

Для выполнения задания достаточно одной виртуальной машины. Запустите все службы (Prometheus, Node exporter, ElasticSearch, Kibana и Fluentbit) на ней.
Не забудьте о лучших практиках написания Ansible ролей:
Роль должна быть идемпотентной. То есть при повторном применении роли к уже настроенному хосту без изменений, роль не должна ничего изменять или ломать.
Используйте хэндлеры для перезагрузки конфигов или перечитывания конфигурации (например Prometheus умеет перечитывать свой конфиг двумя способами. См. https://www.robustperception.io/reloading-prometheus-configuration/)
Выносите все, что может понадобиться конфигурировать пользователю в ваших ролях в defaults. Чтобы как можно меньше нужно было редактировать саму роль в процессе ее использования.
Если вы устанавливаете указанные приложения с помощью пакетных менеджеров, то нет необходимости самостоятельно создавать пользователей, директории и сервис файлы.