# Exercício de Fixação

## Introdução

Neste exercício usaremos uma ferramenta de geração de tráfego entre uma VM cliente e uma VM servidor.

Adotaremos o [iPerf](https://iperf.fr/) como gerador de tráfego, cujo funcionamento é esperado em qualquer Linux e no Windows, embora os comandos foram testados no Ubuntu 20.04.

## Preparativos

### Configuração básica do Vagrantfile com duas VMs em uma rede [hostonly].

```markdown
# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vm.provision "shell", inline: "echo Aloh mundo!!!"

  config.vm.define "vm1" do |vm1|
    vm1.vm.box = "ubuntu/focal64"
    vm1.vm.network "private_network", ip: "192.168.33.10" 
    vm1.vm.provision "shell", inline: "echo Aloh mundo Ubuntu!!!"
  end
  
  config.vm.define "vm2" do |vm2|
    vm2.vm.box = "debian/buster64"
    vm2.vm.network "private_network", ip: "192.168.33.11"
    vm2.vm.provision "shell", inline: "echo Aloh mundo Debian!!!"
  end 
end
```

### Instalação e execução do iPerf:

- Instalação: 
```markdown
  sudo apt update
  sudo apt install -y iperf
```
- Executar o servidor que irá receber o tráfego gerado
```markdown
  sudo iperf -s -D
```

- Executar o cliente que gera tráfego para o servidor
```markdown
  iperf -c [IP_DO_SERVIDOR] -t [TEMPO_EM_SEGUNDOS]
```

## Automatizando a criação do cenário para geração de tráfego

```markdown
# -*- mode: ruby -*-
# vi: set ft=ruby :

NODES = [
    { :hostname => "node1", :ip => "192.168.0.11" },
    { :hostname => "node2", :ip => "192.168.0.12" },
    { :hostname => "controller", :ip => "192.168.0.2" }
]

Vagrant.configure("2") do |config|

  # Do whatever global config here
  config.vm.box = "ubuntu/bionic64"

  NODES.each do |node|

    config.vm.define node[:hostname] do |nodeconfig|

      # Do config that is the same across each node
      nodeconfig.vm.hostname = node[:hostname]
      nodeconfig.vm.network "public_network", bridge: "eno1"
      nodeconfig.vm.network "private_network", ip: node[:ip]
      nodeconfig.vm.provision "shell", inline: <<-SHELL
        echo -e "\n192.168.0.2 controller\n192.168.0.11 node1\n192.168.0.12 node2" | sudo tee -a /etc/hosts
      SHELL

      if node[:hostname] == "controller"
        # Do your provisioning for this machine here
        nodeconfig.vm.provision "shell", inline: <<-SHELL
          echo "Controller"
          echo "git cloning slice-enablers.git"
          git clone https://github.com/dcomp-leris/slice-enablers.git --quiet
          chown -R vagrant.vagrant slice-enablers

          echo "apt install iperf"
          bash slice-enablers/scripts/apt.sh

          echo "iniciando servidor iperf"
          bash slice-enablers/scripts/servidor-iperf.sh
        SHELL

      else
        # Do provisioning for the other machines here
        nodeconfig.vm.provision "shell", inline: <<-SHELL
          echo "Node"
          echo "git cloning slice-enablers.git"
          git clone https://github.com/dcomp-leris/slice-enablers.git --quiet
          chown -R vagrant.vagrant slice-enablers

          echo "apt install iperf"
          bash slice-enablers/scripts/apt.sh
        SHELL

      end

    end

  end
  # Do any global provisioning

end
```

## Referências

Exemplos de configurações do Vagranfile: [Outros Exemplos](../../Exemplos/index.md)

Página principal do Vagrant: [vagrantup.com](https://www.vagrantup.com)