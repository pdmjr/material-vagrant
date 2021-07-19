# Explicando virtualização com o auxílio do Vagrant

## Descrição dos arquivos

Vários arquivos com cenários distintos que serão explicados ao longo desta página.

A maioria destes exemplos de configuração foi retirada do próprio site do [Vagrant](https://vagrantup.com).

Outros links com material sobre o Vagrant:

1. [https://devopscube.com/vagrant-tutorial-beginners/](https://devopscube.com/vagrant-tutorial-beginners/)
2. [https://caiodelgado.dev/vagrant-101/](https://caiodelgado.dev/vagrant-101/)
3. [https://ironlinux.com.br/primeiros-passos-com-vagrant-ubuntu/](https://ironlinux.com.br/primeiros-passos-com-vagrant-ubuntu/)
4. [https://ctrlnotes.com/vagrant-advanced-examples/](https://ctrlnotes.com/vagrant-advanced-examples/)
5. [https://github.com/iJackUA/awesome-vagrant](https://github.com/iJackUA/awesome-vagrant)

(Obs: ao baixar os arquivos dos exemplos a seguir, lembrar de renomear para Vagrantfile apenas.)

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

### Vagrantfile original ([01.Vagrantfile.original](Arquivos/01.Vagrantfile.original))

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

## [Outros exemplos](Exemplos/index.md)

## [Atividade prática de fixação](Atividades/GeracaoTrafego/index.md)
