# Outros Exemplos

## Cenário com múltiplas VMs

### Introdutório ([02.Vagrantfile.multi-intro](../Arquivos/02.Vagrantfile.multi-intro))

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

### A ordem é importante ([03.Vagrantfile.multi-ordering](../Arquivos/03.Vagrantfile.multi-ordering))

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

### Não levantar VM automaticamente ([04.Vagrantfile.multi-autostart](../Arquivos/04.Vagrantfile.multi-autostart))

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

### Criar várias VMs com loop ([05.Vagrantfile.multi-loop](../Arquivos/05.Vagrantfile.multi-loop))

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

### Cenário introdutório ([06.Vagrantfile.net-intro](../Arquivos/06.Vagrantfile.net-intro))

```markdown
# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|

  config.vm.box = "ubuntu/bionic64"
  config.vm.network "private_network", ip: "192.168.33.10"
  config.vm.network "public_network"

end
```

### Criar máquinas em rede com loop ([07.Vagrantfile.net-loop](../Arquivos/07.Vagrantfile.net-loop))

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

### Criar um "cluster" de máquinas em rede ([08.Vagrantfile.net-cluster](../Arquivos/08.Vagrantfile.net-cluster))

```markdown
# -*- mode: ruby -*-
# vi: set ft=ruby :

VAGRANTFILE_API_VERSION = "2"

cluster = {
  "server" => { :ip => "192.168.33.10", :cpus => 1, :mem => 512},
  "client" => { :ip => "192.168.33.11", :cpus => 1, :mem => 512}
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
