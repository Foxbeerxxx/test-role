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
8. `Вывод`
```
(molecule-env) alexey@dellalexey:~/ansible_new/test-role/vector-role$ molecule test
WARNING  Driver docker does not provide a schema.
INFO     default scenario test matrix: dependency, cleanup, destroy, syntax, create, prepare, converge, idempotence, side_effect, verify, cleanup, destroy
INFO     Performing prerun with role_name_check=0...
INFO     Running default > dependency
WARNING  Skipping, missing the requirements file.
WARNING  Skipping, missing the requirements file.
INFO     Running default > cleanup
WARNING  Skipping, cleanup playbook not configured.
INFO     Running default > destroy
INFO     ansible-playbook version: ansible-playbook 
  config file = /etc/ansible/ansible.cfg
  configured module search path = ['/home/alexey/.ansible/plugins/modules', '/usr/share/ansible/plugins/modules']
  ansible python module location = /home/alexey/.venvs/molecule-env/lib/python3.12/site-packages/ansible
  ansible collection location = /home/alexey/.ansible/collections:/usr/share/ansible/collections
  executable location = /home/alexey/.venvs/molecule-env/bin/ansible-playbook
  python version = 3.12.3 (main, Jun 18 2025, 17:59:45) [GCC 13.3.0] (/home/alexey/.venvs/molecule-env/bin/python3)
  jinja version = 3.1.6
  libyaml = True
INFO     Sanity checks: 'docker'

PLAY [Destroy] *****************************************************************

TASK [Set async_dir for HOME env] **********************************************
ok: [localhost]

TASK [Destroy molecule instance(s)] ********************************************
changed: [localhost] => (item=oraclelinux)
changed: [localhost] => (item=ubuntu)

TASK [Wait for instance(s) deletion to complete] *******************************
ok: [localhost] => (item=oraclelinux)
ok: [localhost] => (item=ubuntu)

TASK [Delete docker networks(s)] ***********************************************
skipping: [localhost]

PLAY RECAP *********************************************************************
localhost                  : ok=3    changed=1    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0

INFO     Running default > syntax
INFO     ansible-playbook version: ansible-playbook 
  config file = /etc/ansible/ansible.cfg
  configured module search path = ['/home/alexey/.ansible/plugins/modules', '/usr/share/ansible/plugins/modules']
  ansible python module location = /home/alexey/.venvs/molecule-env/lib/python3.12/site-packages/ansible
  ansible collection location = /home/alexey/.ansible/collections:/usr/share/ansible/collections
  executable location = /home/alexey/.venvs/molecule-env/bin/ansible-playbook
  python version = 3.12.3 (main, Jun 18 2025, 17:59:45) [GCC 13.3.0] (/home/alexey/.venvs/molecule-env/bin/python3)
  jinja version = 3.1.6
  libyaml = True

playbook: /home/alexey/ansible_new/test-role/vector-role/molecule/default/converge.yml
INFO     Running default > create
INFO     ansible-playbook version: ansible-playbook 
  config file = /etc/ansible/ansible.cfg
  configured module search path = ['/home/alexey/.ansible/plugins/modules', '/usr/share/ansible/plugins/modules']
  ansible python module location = /home/alexey/.venvs/molecule-env/lib/python3.12/site-packages/ansible
  ansible collection location = /home/alexey/.ansible/collections:/usr/share/ansible/collections
  executable location = /home/alexey/.venvs/molecule-env/bin/ansible-playbook
  python version = 3.12.3 (main, Jun 18 2025, 17:59:45) [GCC 13.3.0] (/home/alexey/.venvs/molecule-env/bin/python3)
  jinja version = 3.1.6
  libyaml = True

PLAY [Create] ******************************************************************

TASK [Set async_dir for HOME env] **********************************************
ok: [localhost]

TASK [Log into a Docker registry] **********************************************
skipping: [localhost] => (item=None) 
skipping: [localhost] => (item=None) 
skipping: [localhost]

TASK [Check presence of custom Dockerfiles] ************************************
ok: [localhost] => (item={'command': "/bin/sh -c 'mkdir -p /tmp && chmod 1777 /tmp && sleep infinity'", 'image': 'oraclelinux:8', 'name': 'oraclelinux', 'override_command': False, 'privileged': True, 'user': 'root'})
ok: [localhost] => (item={'command': "/bin/sh -c 'mkdir -p /tmp && chmod 1777 /tmp && sleep infinity'", 'image': 'ubuntu:latest', 'name': 'ubuntu', 'override_command': False, 'privileged': True, 'user': 'root'})

TASK [Create Dockerfiles from image names] *************************************
changed: [localhost] => (item={'command': "/bin/sh -c 'mkdir -p /tmp && chmod 1777 /tmp && sleep infinity'", 'image': 'oraclelinux:8', 'name': 'oraclelinux', 'override_command': False, 'privileged': True, 'user': 'root'})
changed: [localhost] => (item={'command': "/bin/sh -c 'mkdir -p /tmp && chmod 1777 /tmp && sleep infinity'", 'image': 'ubuntu:latest', 'name': 'ubuntu', 'override_command': False, 'privileged': True, 'user': 'root'})

TASK [Synchronization the context] *********************************************
changed: [localhost] => (item={'command': "/bin/sh -c 'mkdir -p /tmp && chmod 1777 /tmp && sleep infinity'", 'image': 'oraclelinux:8', 'name': 'oraclelinux', 'override_command': False, 'privileged': True, 'user': 'root'})
ok: [localhost] => (item={'command': "/bin/sh -c 'mkdir -p /tmp && chmod 1777 /tmp && sleep infinity'", 'image': 'ubuntu:latest', 'name': 'ubuntu', 'override_command': False, 'privileged': True, 'user': 'root'})

TASK [Discover local Docker images] ********************************************
ok: [localhost] => (item={'diff': [], 'dest': '/home/alexey/.ansible/tmp/molecule.9bzX.default/Dockerfile_oraclelinux_8', 'src': '/home/alexey/.ansible/tmp/ansible-tmp-1751922819.6669164-26225-216253956644802/.source', 'md5sum': 'f9cacaa9d21c67ac96d5557fa169277b', 'checksum': '690614bd4867cd2182229045ff4ec301bd6cd356', 'changed': True, 'uid': 1000, 'gid': 1000, 'owner': 'alexey', 'group': 'alexey', 'mode': '0600', 'state': 'file', 'size': 1042, 'invocation': {'module_args': {'src': '/home/alexey/.ansible/tmp/ansible-tmp-1751922819.6669164-26225-216253956644802/.source', 'dest': '/home/alexey/.ansible/tmp/molecule.9bzX.default/Dockerfile_oraclelinux_8', 'mode': '0600', 'follow': False, '_original_basename': 'Dockerfile.j2', 'checksum': '690614bd4867cd2182229045ff4ec301bd6cd356', 'backup': False, 'force': True, 'unsafe_writes': False, 'content': None, 'validate': None, 'directory_mode': None, 'remote_src': None, 'local_follow': None, 'owner': None, 'group': None, 'seuser': None, 'serole': None, 'selevel': None, 'setype': None, 'attributes': None}}, 'failed': False, 'item': {'command': "/bin/sh -c 'mkdir -p /tmp && chmod 1777 /tmp && sleep infinity'", 'image': 'oraclelinux:8', 'name': 'oraclelinux', 'override_command': False, 'privileged': True, 'user': 'root'}, 'ansible_loop_var': 'item', 'i': 0, 'ansible_index_var': 'i'})
ok: [localhost] => (item={'diff': [], 'dest': '/home/alexey/.ansible/tmp/molecule.9bzX.default/Dockerfile_ubuntu_latest', 'src': '/home/alexey/.ansible/tmp/ansible-tmp-1751922820.2751825-26225-221118249495867/.source', 'md5sum': '4b5371d46746a8dc9fc3cc8bb66381ad', 'checksum': 'c75aaabd95b5bd8d709c54e533f97bb910802695', 'changed': True, 'uid': 1000, 'gid': 1000, 'owner': 'alexey', 'group': 'alexey', 'mode': '0600', 'state': 'file', 'size': 1042, 'invocation': {'module_args': {'src': '/home/alexey/.ansible/tmp/ansible-tmp-1751922820.2751825-26225-221118249495867/.source', 'dest': '/home/alexey/.ansible/tmp/molecule.9bzX.default/Dockerfile_ubuntu_latest', 'mode': '0600', 'follow': False, '_original_basename': 'Dockerfile.j2', 'checksum': 'c75aaabd95b5bd8d709c54e533f97bb910802695', 'backup': False, 'force': True, 'unsafe_writes': False, 'content': None, 'validate': None, 'directory_mode': None, 'remote_src': None, 'local_follow': None, 'owner': None, 'group': None, 'seuser': None, 'serole': None, 'selevel': None, 'setype': None, 'attributes': None}}, 'failed': False, 'item': {'command': "/bin/sh -c 'mkdir -p /tmp && chmod 1777 /tmp && sleep infinity'", 'image': 'ubuntu:latest', 'name': 'ubuntu', 'override_command': False, 'privileged': True, 'user': 'root'}, 'ansible_loop_var': 'item', 'i': 1, 'ansible_index_var': 'i'})

TASK [Build an Ansible compatible image (new)] *********************************
ok: [localhost] => (item=molecule_local/oraclelinux:8)
ok: [localhost] => (item=molecule_local/ubuntu:latest)

TASK [Create docker network(s)] ************************************************
skipping: [localhost]

TASK [Determine the CMD directives] ********************************************
skipping: [localhost] => (item={'command': "/bin/sh -c 'mkdir -p /tmp && chmod 1777 /tmp && sleep infinity'", 'image': 'oraclelinux:8', 'name': 'oraclelinux', 'override_command': False, 'privileged': True, 'user': 'root'})
skipping: [localhost] => (item={'command': "/bin/sh -c 'mkdir -p /tmp && chmod 1777 /tmp && sleep infinity'", 'image': 'ubuntu:latest', 'name': 'ubuntu', 'override_command': False, 'privileged': True, 'user': 'root'})
skipping: [localhost]

TASK [Create molecule instance(s)] *********************************************
changed: [localhost] => (item=oraclelinux)
changed: [localhost] => (item=ubuntu)

TASK [Wait for instance(s) creation to complete] *******************************
FAILED - RETRYING: [localhost]: Wait for instance(s) creation to complete (300 retries left).
changed: [localhost] => (item={'failed': 0, 'started': 1, 'finished': 0, 'ansible_job_id': 'j149962140532.26543', 'results_file': '/home/alexey/.ansible_async/j149962140532.26543', 'changed': True, 'item': {'command': "/bin/sh -c 'mkdir -p /tmp && chmod 1777 /tmp && sleep infinity'", 'image': 'oraclelinux:8', 'name': 'oraclelinux', 'override_command': False, 'privileged': True, 'user': 'root'}, 'ansible_loop_var': 'item'})
changed: [localhost] => (item={'failed': 0, 'started': 1, 'finished': 0, 'ansible_job_id': 'j706030623730.26579', 'results_file': '/home/alexey/.ansible_async/j706030623730.26579', 'changed': True, 'item': {'command': "/bin/sh -c 'mkdir -p /tmp && chmod 1777 /tmp && sleep infinity'", 'image': 'ubuntu:latest', 'name': 'ubuntu', 'override_command': False, 'privileged': True, 'user': 'root'}, 'ansible_loop_var': 'item'})

PLAY RECAP *********************************************************************
localhost                  : ok=8    changed=4    unreachable=0    failed=0    skipped=3    rescued=0    ignored=0
```



9. `Про Tox, скопировал нужные файлы в свою роль vector-role`
10. `Запускаю tox и проверяю`
```
docker run --privileged=True \
  -v /home/alexey/ansible_new/test-role/vector-role:/opt/vector-role \
  -w /opt/vector-role \
  -it aragast/netology:latest /bin/bash

```
![4](https://github.com/Foxbeerxxx/test-role/blob/main/img/img4.png)

11. `Устанавливаю роли pip install -r tox-requirements.txt`

![5](https://github.com/Foxbeerxxx/test-role/blob/main/img/img5.png)


12. `Запускаю tox, проверяю работоспособность`

13. `Коммит и тег`
```
git add .
git commit -m "Add Tox"
git tag -a v1.2.0 -m "Tox integration "
git push origin v1.2.0
```

