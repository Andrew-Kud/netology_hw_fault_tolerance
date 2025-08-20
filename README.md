# Домашнее задание к занятию "`Отказоустойчивость в облаке`" - `Кудряшов Андрей`


### Инструкция по выполнению домашнего задания

   1. Сделайте `fork` данного репозитория к себе в Github и переименуйте его по названию или номеру занятия, например, https://github.com/имя-вашего-репозитория/git-hw или  https://github.com/имя-вашего-репозитория/7-1-ansible-hw).
   2. Выполните клонирование данного репозитория к себе на ПК с помощью команды `git clone`.
   3. Выполните домашнее задание и заполните у себя локально этот файл README.md:
      - впишите вверху название занятия и вашу фамилию и имя
      - в каждом задании добавьте решение в требуемом виде (текст/код/скриншоты/ссылка)
      - для корректного добавления скриншотов воспользуйтесь [инструкцией "Как вставить скриншот в шаблон с решением](https://github.com/netology-code/sys-pattern-homework/blob/main/screen-instruction.md)
      - при оформлении используйте возможности языка разметки md (коротко об этом можно посмотреть в [инструкции  по MarkDown](https://github.com/netology-code/sys-pattern-homework/blob/main/md-instruction.md))
   4. После завершения работы над домашним заданием сделайте коммит (`git commit -m "comment"`) и отправьте его на Github (`git push origin`);
   5. Для проверки домашнего задания преподавателем в личном кабинете прикрепите и отправьте ссылку на решение в виде md-файла в вашем Github.
   6. Любые вопросы по выполнению заданий спрашивайте в чате учебной группы и/или в разделе “Вопросы по заданию” в личном кабинете.
   
Желаем успехов в выполнении домашнего задания!
   
### Дополнительные материалы, которые могут быть полезны для выполнения задания

1. [Руководство по оформлению Markdown файлов](https://gist.github.com/Jekins/2bf2d0638163f1294637#Code)

---

### Задание 1

В качестве результата пришлите:

1. Terraform Playbook.
2. Скриншот статуса балансировщика и целевой группы.
3. Скриншот страницы, которая открылась при запросе IP-адреса балансировщика.

Я так понял в 1 задании имеется ввиду Terraform main.tf и ansible playbook?

terraform main.tf:
```
terraform {
  required_providers {
    yandex = {
      source = "yandex-cloud/yandex"
    }
  }
}


provider "yandex" {
  zone = "ru-central1-b"
}


resource "yandex_compute_instance" "vm" {
  count = 2

  name        = "vm${count.index}"
  platform_id = "standard-v1"
  boot_disk {
    initialize_params {
      image_id = "fd87j6d92jlrbjqbl32q"
      size     = 8
    }
  }

  network_interface {
    subnet_id = yandex_vpc_subnet.subnet1.id
    nat       = true
  }

  resources {
    cores         = 2
    memory        = 2
    core_fraction = 20
  }

metadata = { ssh-keys = "ubuntu:${file("~/.ssh/id_ed25519.pub")}" }
}


resource "yandex_vpc_network" "network1" {
  name = "network1"
}


resource "yandex_vpc_subnet" "subnet1" {
  name           = "subnet1"
  v4_cidr_blocks = ["172.24.8.0/24"]
  network_id     = yandex_vpc_network.network1.id
}


resource "yandex_lb_target_group" "group1" {
  name = "group1"

  dynamic "target" {
    for_each = yandex_compute_instance.vm
    content {
      subnet_id = yandex_vpc_subnet.subnet1.id
      address   = target.value.network_interface.0.ip_address
    }
  }
}


resource "yandex_lb_network_load_balancer" "balancer1" {
  name                = "balancer1"
  deletion_protection = "false"
  listener {
    name = "my-lb1"
    port = 80
    external_address_spec {
      ip_version = "ipv4"
    }
  }

 attached_target_group {
    target_group_id = yandex_lb_target_group.group1.id
    healthcheck {
      name = "http"
      http_options {
        port = 80
        path = "/"
      }
    }
  }
}

```

Ansible:
```
---
- name: installysing_Nginx
  hosts: all
  become: yes
  gather_facts: yes

  tasks:
    - name: cace_update
      apt:
        update_cache: yes
        cache_valid_time: 3600

    - name: install_Nginx
      apt:
        name: nginx
        state: present

    - name: start_Nginx
      systemd:
        name: nginx
        state: started
        enabled: yes

    - name: status-check_Nginx
      command: systemctl is-active nginx
      register: nginx_status
      changed_when: false

    - name: Nginx_interactiv_status
      debug:
        msg: "Nginx_ACTIVE: {{ nginx_status.stdout }}"

    - name: generate_info_nginx
      copy:
        content: |
          Server: {{ inventory_hostname }}
          External IP: {{ ansible_host }}
          Internal IP: {{ ansible_default_ipv4.address }}
        dest: /var/www/html/info
        mode: '0644'

```


Скриншот балансировщика:

<img width="819" height="798" alt="1" src="https://github.com/user-attachments/assets/5fcecadc-6272-4fb6-8ddb-7e25e9cd4724" />

<img width="1812" height="288" alt="2" src="https://github.com/user-attachments/assets/dbe13abe-31b9-4581-ae89-f41794c2ee7a" />


Скриншот запроса:

<img width="983" height="579" alt="3" src="https://github.com/user-attachments/assets/24c0a1e3-b984-4401-b9d5-679ed33cbfc0" />

<img width="617" height="175" alt="4" src="https://github.com/user-attachments/assets/07c82abc-21fc-46b3-8674-736020a1dba2" />


---

### Задание 2

`Приведите ответ в свободной форме........`

1. `Заполните здесь этапы выполнения, если требуется ....`
2. `Заполните здесь этапы выполнения, если требуется ....`
3. `Заполните здесь этапы выполнения, если требуется ....`
4. `Заполните здесь этапы выполнения, если требуется ....`
5. `Заполните здесь этапы выполнения, если требуется ....`
6. 

```
Поле для вставки кода...
....
....
....
....
```

`При необходимости прикрепитe сюда скриншоты
![Название скриншота 2](ссылка на скриншот 2)`


---

### Задание 3

`Приведите ответ в свободной форме........`

1. `Заполните здесь этапы выполнения, если требуется ....`
2. `Заполните здесь этапы выполнения, если требуется ....`
3. `Заполните здесь этапы выполнения, если требуется ....`
4. `Заполните здесь этапы выполнения, если требуется ....`
5. `Заполните здесь этапы выполнения, если требуется ....`
6. 

```
Поле для вставки кода...
....
....
....
....
```

`При необходимости прикрепитe сюда скриншоты
![Название скриншота](ссылка на скриншот)`

### Задание 4

`Приведите ответ в свободной форме........`

1. `Заполните здесь этапы выполнения, если требуется ....`
2. `Заполните здесь этапы выполнения, если требуется ....`
3. `Заполните здесь этапы выполнения, если требуется ....`
4. `Заполните здесь этапы выполнения, если требуется ....`
5. `Заполните здесь этапы выполнения, если требуется ....`
6. 

```
Поле для вставки кода...
....
....
....
....
```

`При необходимости прикрепитe сюда скриншоты
![Название скриншота](ссылка на скриншот)`
