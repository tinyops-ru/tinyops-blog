+++
title = "Как починить ошибку Inaccessible VM в VirtualBox и Vagrant"
description = "Как вывести виртуальную машину Vagrant из состояния ошибки 'inaccessible vm'."
date = 2020-05-10
updated = 2023-07-27

[taxonomies]
tags = ["virtualbox", "vagrant"]
categories = ["troubleshooting"]
+++

Поймал эту проблему однажды когда поднимал виртуальную машину с помощью Vagrant:

> Your VM has become "inaccessible". Unfortunately, this is a critical error with VirtualBox that Vagrant 
> can not cleanly recover from. Please open VirtualBox and clear out your 
> inaccessible virtual machines or find a way to fix them.

Virtual Box маркирует виртуальную машину как `inaccessible` если не может её запустить и 
не может сформулировать пользователю в чем же дело.

Умники в интернетах конечно же предлагаю пересоздать машину с нуля, наподобие умников на форумах Microsoft. 
Вторые, при малейшем недомогании, обычно рекомендуют переустановить ОС :)

Для начала посмотрим список виртуальных машин:

```bash
$ VBoxManage list vms

"prometheus_default_1562491030033_47028" {b8c2eec3-e3e2-4d7b-9ee4-9f09d7691a71}
"bamboo-agent-centos7_default_1568705322865_68355" {fcc600be-859a-430e-8da2-f3ff8dfa2d9f}
"<inaccessible>" {4fb4e265-61cb-4ce8-d3c4-de5f2daea7f8}
"supervisor-jolokia_default_1572425247238_89749" {ee47977e-11f2-47b4-a2af-756cfff524f2}
```

Запоминаем уникальный ID проблемной виртуальной машины - `4fb4e265-61cb-4ce8-d3c4-de5f2daea7f8`. 

Далее удаляем регистрацию виртуальной машины в недрах VirtualBox:

```bash
VBoxManage unregistervm 4fb4e265-61cb-4ce8-d3c4-de5f2daea7f8
```

И снова регистрируем:

```bash
VBoxManage registervm 4fb4e265-61cb-4ce8-d3c4-de5f2daea7f8
```

Теперь можно поднять машину как и планировали первоначально:

```bash
vagrant up
```

### Что делать если у меня несколько проблемных машин?

Бывают ситуации когда машин со статусом `inaccessible` может быть более чем одна. Можно перерегистрировать все, либо
найти UUID для проблемной машины.

**Решение для Vagrant:**

Заходим в каталог с Vagrantfile и читаем UUID:

```bash
$ cat .vagrant/machines/default/virtualbox/id

4fb4e265-61cb-4ce8-d3c4-de5f2daea7f8
```

На этом все.
