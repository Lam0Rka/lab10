# lab10
## Laboratory work X

Данная лабораторная работа посвещена изучению процесса создания и конфигурирования виртуальной среды разработки с использованием **Vagrant**

```sh
$ open https://www.vagrantup.com/intro/index.html
```
Определение: 
Vagrant-это инструмент для создания и управления средами виртуальных машин в рамках одного рабочего процесса. Благодаря простому в использовании рабочему процессу и сосредоточенности на автоматизации, Vagrant сокращает время настройки среды разработки, увеличивает четность производства и делает оправдание "работы на моей машине" пережитком прошлого.

## Tasks

- [x] 1. Ознакомиться со ссылками учебного материала
- [x] 2. Выполнить инструкцию учебного материала
- [x] 3. Составить отчет и отправить ссылку личным сообщением в **Slack**

## Tutorial
 Устанавливаем значение переменных окружения
 Указываем имя пользователя Github
 Указываем используемый пакетный менеджер 
```sh
$ export GITHUB_USERNAME=<имя_пользователя>
$ export PACKAGE_MANAGER=<пакетный_менеджер>
```
Переходим в рабочую директорию Устанавливаем vagrant
```sh
$ cd ${GITHUB_USERNAME}/workspace
$ ${PACKAGE_MANAGER} install vagrant
```
Дополнительно: Если использовать vagrant появится справка со всеми доступными подкомандами. Можно запустить  vagrant  флагом-h, чтобы вывести справку об этой конкретной команде. Например, попробуйте запустить vagrant init-h. Справка выведет краткое описание того, что делает команда, в одном предложении, а также список всех флагов, которые принимает команда.

6 Выводим версию скаченного vagrant
7 Создаем новую виртуальную машину
8 Выводим содержимое Vagrantfile

```sh
$ vagrant version
$ vagrant init bento/ubuntu-19.10
$ less Vagrantfile
$ vagrant init -f -m bento/ubuntu-19.10
```
 Создаем директорию shared

```sh
$ mkdir shared
```
 В файл Vagrantfile записываем комманды для запуска скрипта 
```sh
$ cat > Vagrantfile <<EOF
\$script = <<-SCRIPT
sudo apt install docker.io -y
sudo docker pull fastide/ubuntu:19.04
sudo docker create -ti --name fastide fastide/ubuntu:19.04 bash
sudo docker cp fastide:/home/developer /home/
sudo useradd developer
sudo usermod -aG sudo developer
echo "developer:developer" | sudo chpasswd
sudo chown -R developer /home/developer
SCRIPT
EOF
```
В файл Vagrantfile записываем конфигурацию для виртуальной машины
 vagrant-vbguest - это плагин, который автоматически обновляет гостевые дополнения VirtualBox
```sh
$ cat >> Vagrantfile <<EOF

Vagrant.configure("2") do |config|

  config.vagrant.plugins = ["vagrant-vbguest"]
EOF
```
Продолжаем конфигурацию для виртуальной машины
```sh
$ cat >> Vagrantfile <<EOF

  config.vm.box = "bento/ubuntu-19.10"
  config.vm.network "public_network"
  config.vm.synced_folder('shared', '/vagrant', type: 'rsync')

  config.vm.provider "virtualbox" do |vb|
    vb.gui = true
    vb.memory = "2048"
  end

  config.vm.provision "shell", inline: \$script, privileged: true

  config.ssh.extra_args = "-tt"

end
EOF
```

 Проверка корректности файла Vagrantfile
 Просмотрим список вируальных машин и их статусы
 Запуск виртуальной машины 
 Информация о проброске портов
 Подключение по ssh к запущенной виртуальной машине
 Посмотреть список сохранённых состояний виртальной машины
 Остановить виртуальную машину
 Востановить состояние виртуальной машины по снимку

```sh
$ vagrant validate

$ vagrant status
$ vagrant up # --provider virtualbox
$ vagrant port
$ vagrant status
$ vagrant ssh

$ vagrant snapshot list
$ vagrant snapshot push
$ vagrant snapshot list
$ vagrant halt
$ vagrant snapshot pop
```
Настраиваем Vagrant для работы с VMware
```ruby
  config.vm.provider :vmware_esxi do |esxi|

    esxi.esxi_hostname = '<exsi_hostname>'
    esxi.esxi_username = 'root'
    esxi.esxi_password = 'prompt:'

    esxi.esxi_hostport = 22

    esxi.guest_name = '${GITHUB_USERNAME}'

    esxi.guest_username = 'vagrant'
    esxi.guest_memsize = '2048'
    esxi.guest_numvcpus = '2'
    esxi.guest_disk_type = 'thin'
  end
```

```sh
$ vagrant plugin install vagrant-vmware-esxi
$ vagrant plugin list
$ vagrant up --provider=vmware_esxi
```

## Links

- [VirualBox](https://www.virtualbox.org/)
- [Vagrant providers](https://github.com/hashicorp/vagrant/wiki/Available-Vagrant-Plugins#providers)
- [Vagrant vbguest plugin](https://github.com/dotless-de/vagrant-vbguest)
- [Vagrant disksize plugin](https://github.com/sprotheroe/vagrant-disksize)
- [Vagrant vmware esxi plugin](https://github.com/josenk/vagrant-vmware-esxi)

```
Copyright (c) 2015-2021 The ISC Authors
```
