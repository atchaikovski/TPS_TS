# общий README для всех окружений. 

КРАТКО:
1. заполнение скрипта env.sh соответствующими значениями переменных  
2. source ./env.sh
3. обновление/заполнение необходимыми бинарниками каталога SW
4. проверка/обновление переменных в файле main.yml в каталоге vars/
5. переход в каталог с нужным окружением
6. заполнение файла hosts (если будет использоваться vagrant) ==> vagrant up
7. заполнение файла inventory (для ansible)
8. переход в общий каталог
9. запуск ansible-playbook -i "environment"/inventory -u devops "task".yml  

---

# задумывалось как комплексная обработка развертывания инфраструктур ТПС-ТС
путем создания ВМ с помощью vagrant, затем вызова ansible для окончательного конфигурирования каждой ВМ под определенную задачу
обнаружилось, что vSphere не поддерживает кастомизацию Debian OS...
когда эта проблема будет решена, то можно будет вызывать vagrant с определенным файлом 
конфигурации и затем, по итогу создания набора ВМ, ansible для финализации

# TODO:
  - в идеале, заменить файлы hosts и inventory на 1 файл inventory. нужно будет парсить строку ansible_host="ip" и забирать из нее только "ip". тогда от файла hosts в vagrant можно отказаться 

Vagrant на данный момент закрыт в России, нужно устанавливать через VPN. для работы скриптов нужен vsphere provider: https://github.com/nsidc/vagrant-vsphere

!! Запуск vagrant производится после установки всех переменных окружения с помощью скрипта env.sh ==> source ./env.sh, так как в нем

vagrant up в каждом каталоге "environment":
  - prod
  - test1
  - test2

запуск ansible playbooks:
# можно включить provisioning для каждой категории <environment> и вызывать вместе с vagrant, пока так не сделано

# отдельно по задачам сделаны playbooks

ad - добавляет ВМ to AD
    ansible-playbook -u devops -i "environment"/inventory ad.yml

postgres - устанавливает PostgreSQL 13.5 (требование разработчиков из Лимон) 
    ansible-playbook -u devops -i "environment"/inventory postgres.yml

mariadb - устанавливает стандартную MariaDB (mysql) из AstraLinux repo
    ansible-playbook -u devops -i "environment"/inventory mariadb.yml

bitrix - устанавливает из исходников php (заданной версии), копирует и разворачивает 1С-Битрикс + nginx, ждет дальнейшей настройки 1С-Битрикс
    ansible-playbook -u devops -i "environment"/inventory bitrix.yml

--> в работе: deckhouse - устанавливает мастер узел deckhouse, формирует набор скриптов для workers, ingress, system и копирует на соответствующие узлы, затем запускает скрипты. дополнительные мастер-узлы затем надо добавлять вручную (?) 
    ansible-playbook -u devops -i "environment"/inventory deckhouse.yml
