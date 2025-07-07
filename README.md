# Домашнее задание к занятию "`Тестирование роли`" - `Татаринцев А.А.`




### Задание 1



1. `Установка molecule и драйверов`
```
pip3 install "molecule" "molecule[docker]" "molecule_podman"
```
2. `Подгрузка образа`
```
docker pull aragast/netology:latest
Этот образ содержит нужные версии Python, podman, tox и другие инструменты.
```
![1](https://github.com/Foxbeerxxx/test-role/blob/main/img/img1.png)

3. `Активируем виртуальное окружение`
```
source ~/.venvs/molecule-env/bin/activate
```

4. `Molecule-сценарий успешно создан с драйвером Docker`
```
molecule init scenario default --driver-name docker

```


5. `Меняю файл molecule.yml`
```
---
driver:
  name: docker

platforms:
  - name: oraclelinux
    image: oraclelinux:8
    command: /sbin/init
    privileged: true

  - name: ubuntu
    image: ubuntu:latest
    command: /sbin/init
    privileged: true

```
6. `Создаю verify.yml  (~/ansible_new/test-role/vector-role/molecule/default/verify.yml)  и добавляю наполнение`
```
---
- name: Verify Vector installation
  hosts: all
  gather_facts: false
  tasks:

    - name: Assert that vector binary exists
      command: which vector
      register: vector_binary
      failed_when: vector_binary.rc != 0

    - name: Validate vector config file
      command: vector validate --config-yaml /etc/vector/vector.yaml
      register: config_check
      failed_when: config_check.rc != 0
      ignore_errors: yes  # systemd может не работать в контейнере

    - name: Check vector service is running (optional)
      command: systemctl is-active vector
      register: service_status
      failed_when: service_status.stdout != "active"
      ignore_errors: yes

```

7. `Запускаю полный тест `
```
molecule test
```


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
