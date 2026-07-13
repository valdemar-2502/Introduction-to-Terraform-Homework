# Домашнее задание к занятию «Введение в Terraform» - Kadancev Vladimir

### Цели задания

1. Установить и настроить Terrafrom.
2. Научиться использовать готовый код.

------

### Чек-лист готовности к домашнему заданию

1. Скачайте и установите **Terraform** версии >=1.12.0 . Приложите скриншот вывода команды ```terraform --version```.
---
### Ответ:
![terraform](https://github.com/valdemar-2502/Introduction-to-Terraform-Homework/blob/main/screenshots/task0.png)

2. Скачайте на свой ПК этот git-репозиторий. Исходный код для выполнения задания расположен в директории **01/src**.
3. Убедитесь, что в вашей ОС установлен docker.
------

### Инструменты и дополнительные материалы, которые пригодятся для выполнения задания

1. Репозиторий с ссылкой на зеркало для установки и настройки Terraform: [ссылка](https://github.com/netology-code/devops-materials).
2. Установка docker: [ссылка](https://docs.docker.com/engine/install/ubuntu/). 
------
### Внимание!! Обязательно предоставляем на проверку получившийся код в виде ссылки на ваш github-репозиторий!
------
---
### Задание 1

1. Перейдите в каталог [**src**](https://github.com/netology-code/ter-homeworks/tree/main/01/src). Скачайте все необходимые зависимости, использованные в проекте. 
---
### Ответ:
``
![terraform](https://github.com/valdemar-2502/Introduction-to-Terraform-Homework/blob/main/screenshots/task0-1.png)
![terraform](https://github.com/valdemar-2502/Introduction-to-Terraform-Homework/blob/main/screenshots/task0-2.png)

---
2. Изучите файл **.gitignore**. В каком terraform-файле, согласно этому .gitignore, допустимо сохранить личную, секретную информацию?(логины,пароли,ключи,токены итд)
---
### Ответ:
Согласно .gitignore, личную секретную информацию можно сохранить в файле:
personal.auto.tfvars
Файлы с расширением .auto.tfvars автоматически загружаются Terraform.

---
3. Выполните код проекта. Найдите  в state-файле секретное содержимое созданного ресурса **random_password**, пришлите в качестве ответа конкретный ключ и его значение.
---
### Ответ:
![terraform](https://github.com/valdemar-2502/Introduction-to-Terraform-Homework/blob/main/screenshots/task1.png)

4. Раскомментируйте блок кода, примерно расположенный на строчках 29–42 файла **main.tf**.
Выполните команду ```terraform validate```. Объясните, в чём заключаются намеренно допущенные ошибки. Исправьте их.
---
### Ответ:
![terraform](https://github.com/valdemar-2502/Introduction-to-Terraform-Homework/blob/main/screenshots/task2.png)
### Ошибки:

```
random_password: имя ресурса содержит заглавные буквы в "resulT" вместо "result"
docker_container: имя "1nginx" начинается с цифры, что недопустимо
name  = "example_${random_password.random_string_FAKE.resulT}" не допустимо "_FAKE"
```

5. Выполните код. В качестве ответа приложите: исправленный фрагмент кода и вывод команды ```docker ps```.

---
### Ответ:
![terraform](https://github.com/valdemar-2502/Introduction-to-Terraform-Homework/blob/main/screenshots/task3.png)

```
resource "docker_image" "nginx" {
  name         = "nginx:latest"
  keep_locally = true
}

resource "docker_container" "nginx" {
  image = docker_image.nginx.image_id
  name  = "example_${random_password.random_string.result}"

  ports {
    internal = 80
    external = 9090
  }
}

```
---
``
![terraform](https://github.com/valdemar-2502/Introduction-to-Terraform-Homework/blob/main/screenshots/task4.png)

6. Замените имя docker-контейнера в блоке кода на ```hello_world```. Не перепутайте имя контейнера и имя образа. Мы всё ещё продолжаем использовать name = "nginx:latest". Выполните команду ```terraform apply -auto-approve```.
Объясните своими словами, в чём может быть опасность применения ключа  ```-auto-approve```. Догадайтесь или нагуглите зачем может пригодиться данный ключ? В качестве ответа дополнительно приложите вывод команды ```docker ps```.
---
### Ответ:

```
resource "docker_container" "nginx" {
  image = docker_image.nginx.image_id
  name  = "hello_world"

  ports {
    internal = 80
    external = 9090
  }
}
```

### Ключ -auto-approve автоматически подтверждает все изменения без запроса подтверждения пользователя. Это опасно, так как:

Можно случайно удалить production-ресурсы

Изменения применяются немедленно без возможности проверки плана

Нет времени на осознание последствий изменений

### Где может пригодиться:

В CI/CD пайплайнах для автоматического развертывания

В скриптах автоматизации

При тестировании в изолированных средах
![terraform](https://github.com/valdemar-2502/Introduction-to-Terraform-Homework/blob/main/screenshots/task6.png)

7. Уничтожьте созданные ресурсы с помощью **terraform**. Убедитесь, что все ресурсы удалены. Приложите содержимое файла **terraform.tfstate**. 
---
![terraform](https://github.com/valdemar-2502/Introduction-to-Terraform-Homework/blob/main/screenshots/task5.png)
8. Объясните, почему при этом не был удалён docker-образ **nginx:latest**. Ответ **ОБЯЗАТЕЛЬНО НАЙДИТЕ В ПРЕДОСТАВЛЕННОМ КОДЕ**, а затем **ОБЯЗАТЕЛЬНО ПОДКРЕПИТЕ** строчкой из документации [**terraform провайдера docker**](https://library.tf/providers/kreuzwerker/docker/latest).  (ищите в классификаторе resource docker_image )

---
### Ответ:
Образ не удален, потому что в ресурсе docker_image установлен параметр:
```
keep_locally = true
Этот параметр указывает Terraform не удалять образ при уничтожении ресурса.

Подтверждение из документации провайдера docker:
Согласно документации resource docker_image, параметр keep_locally:

"If true, then the Docker image won't be deleted on destroy operation. If this is false, it will delete the image from the docker local storage on destroy operation."

Таким образом, keep_locally = true предотвращает удаление образа nginx:latest при выполнении terraform destroy.
---
```
[ссылка](https://library.tf/providers/kreuzwerker/docker/latest/docs/resources/image)
---
------

## Дополнительное задание (со звёздочкой*)

**Настоятельно рекомендуем выполнять все задания со звёздочкой.** Они помогут глубже разобраться в материале.   
Задания со звёздочкой дополнительные, не обязательные к выполнению и никак не повлияют на получение вами зачёта по этому домашнему заданию. 

### Задание 2*

1. Создайте в облаке ВМ. Сделайте это через web-консоль, чтобы не слить по незнанию токен от облака в github(это тема следующей лекции). Если хотите - попробуйте сделать это через terraform, прочитав документацию yandex cloud. Используйте файл ```personal.auto.tfvars``` и гитигнор или иной, безопасный способ передачи токена!
2. Подключитесь к ВМ по ssh и установите стек docker.
3. Найдите в документации docker provider способ настроить подключение terraform на вашей рабочей станции к remote docker context вашей ВМ через ssh.
4. Используя terraform и  remote docker context, скачайте и запустите на вашей ВМ контейнер ```mysql:8``` на порту ```127.0.0.1:3306```, передайте ENV-переменные. Сгенерируйте разные пароли через random_password и передайте их в контейнер, используя интерполяцию из примера с nginx.(```name  = "example_${random_password.random_string.result}"```  , двойные кавычки и фигурные скобки обязательны!) 
```
    environment:
      - "MYSQL_ROOT_PASSWORD=${...}"
      - MYSQL_DATABASE=wordpress
      - MYSQL_USER=wordpress
      - "MYSQL_PASSWORD=${...}"
      - MYSQL_ROOT_HOST="%"
```

6. Зайдите на вашу ВМ , подключитесь к контейнеру и проверьте наличие секретных env-переменных с помощью команды ```env```. Запишите ваш финальный код в репозиторий.

### Задание 3*
1. Установите [opentofu](https://opentofu.org/)(fork terraform с лицензией Mozilla Public License, version 2.0) любой версии
2. Попробуйте выполнить тот же код с помощью ```tofu apply```, а не terraform apply.
------

### Правила приёма работы

Домашняя работа оформляется в отдельном GitHub-репозитории в файле README.md.   
Выполненное домашнее задание пришлите ссылкой на .md-файл в вашем репозитории.

### Критерии оценки

Зачёт ставится, если:

* выполнены все задания,
* ответы даны в развёрнутой форме,
* приложены соответствующие скриншоты и файлы проекта,
* в выполненных заданиях нет противоречий и нарушения логики.

На доработку работу отправят, если:

* задание выполнено частично или не выполнено вообще,
* в логике выполнения заданий есть противоречия и существенные недостатки. 
