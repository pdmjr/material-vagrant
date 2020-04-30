# Explicando virtualização com o auxílio do Vagrant

## Descrição dos arquivos

Vários arquivos com cenários distintos que serão explicados ao longo desta página.

A maioria destes exemplos de configuração foi retirada do próprio site do [Vagrant](https://vagrantup.com).

## Instalação do Virtualbox e do Vagrant

```markdown
$ sudo apt update
...
$ sudo apt -y install virtualbox
...
$ sudo apt -y install vagrant
...
```

## Iniciando com o Vagrant

```markdown
$ vagrant init ubuntu/bionic64
A `Vagrantfile` has been placed in this directory. You are now
ready to `vagrant up` your first virtual environment! Please read
the comments in the Vagrantfile as well as documentation on
`vagrantup.com` for more information on using Vagrant.
$
```

### Vagrantfile original ([Vagrantfile1.original](Vagrantfile1.original)) 
```markdown
# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure("2") do |config|
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://vagrantcloud.com/search.
  config.vm.box = "ubuntu/bionic64"

...
end
```

## Cenário com múltiplas VMs

### Introdutório ([Vagrantfile2.multi-intro](Vagrantfile2.multi-intro))

```markdown
# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vm.provision "shell", inline: "echo Aloh mundo!!!"

  config.vm.define "vm1" do |vm1|
    vm1.vm.box = "ubuntu/bionic64"
  end

  config.vm.define "vm2" do |vm2|
    vm2.vm.box = "centos/7"
  end
end
```

### A ordem é importante ([Vagrantfile3.multi-ordering](Vagrantfile3.multi-ordering))

```markdown
# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/bionic64"
  config.vm.provision :shell, inline: "echo A"

  config.vm.define "vm1" do |vm1|
    vm1.vm.provision :shell, inline: "echo B"
  end

#  config.vm.define "vm2" do |vm2|
#    vm2.vm.provision :shell, inline: "echo C"
#  end

  config.vm.provision :shell, inline: "echo D"
end
```

### Não levantar VM automaticamente ([Vagrantfile4.multi-autostart](Vagrantfile4.multi-autostart))

```markdown
# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|

  config.vm.box = "ubuntu/bionic64"

  config.vm.define "vm1"

  config.vm.define "vm2"

  config.vm.define "vm3", autostart: false

end
```

### Criar várias VMs com loop ([Vagrantfile5.multi-loop](Vagrantfile5.multi-loop))

```markdown
# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/bionic64"

  (1..3).each do |i|
    config.vm.define "vm#{i}" do |vms|
      vms.vm.provision "shell", inline: "echo Aloh mundo da vm #{i}"
    end
  end

end
```

## Cenários com configurações de rede

### Cenário introdutório ([Vagrantfile6.net-intro](Vagrantfile6.net-intro))

```markdown
# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|

  config.vm.box = "ubuntu/bionic64"
  config.vm.network "private_network", ip: "192.168.33.10"
  config.vm.network "public_network"

end
```

### Criar máquinas em rede com loop ([Vagrantfile7.net-loop](Vagrantfile7.net-loop))

```markdown
# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/bionic64"

  (1..3).each do |i|
    config.vm.define "vm#{i}" do |vms|
      vms.vm.provision "shell", inline: "echo Aloh mundo da vm #{i}"
      vms.vm.provider "virtualbox" do |vb|
        vb.memory = "512"
        vb.cpus = "1"
      end
      vms.vm.hostname = "vm#{i}"
      vms.vm.network "public_network", bridge: "eno1"
      vms.vm.network "private_network", ip: "192.168.1.#{i}"
    end
  end

end
```

### Criar um "cluster" de máquinas em rede ([Vagrantfile8.net-cluster](Vagrantfile8.net-cluster))

```markdown
# -*- mode: ruby -*-
# vi: set ft=ruby :

VAGRANTFILE_API_VERSION = "2"

cluster = {
  "master" => { :ip => "192.168.33.10", :cpus => 1, :mem => 512},
  "slave" => { :ip => "192.168.33.11", :cpus => 1, :mem => 512}
}

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|

  config.vm.box = "ubuntu/bionic64"

  cluster.each_with_index do |(hostname, info), index|

    config.vm.define hostname do |cfg|

      cfg.vm.provider :virtualbox do |vb, override|

        override.vm.network :private_network, ip: "#{info[:ip]}"
        override.vm.hostname = hostname
        vb.name = hostname
        vb.customize ["modifyvm", :id, "--memory", info[:mem], "--cpus", info[:cpus], "--hwvirtex", "on"]

      end # end provider

    end # end define

  end # end cluster

end # end config
```
