---

# Cluster - iniciar Consul + Vault + Nomad + amigos

[![Build Status](https://jenkins.liquiddemo.org/api/badges/liquidinvestigations/cluster/status.svg)](https://jenkins.liquiddemo.org/liquidinvestigations/cluster)

Este script instala e configura [Consul][], [Vault][] e [Nomad][]. Ele usa Docker para empacotar esses serviços, já que isso não é suportado pela Hashicorp.

[consul]: https://www.consul.io/
[vault]: https://www.vaultproject.io/
[nomad]: https://www.nomadproject.io/
[supervisord]: http://supervisord.org/

## Início rápido

Instale e deixe o `Docker` em execução. Siga as instruções em
[`get.docker.com`](https://docs.docker.com/install/linux/docker-ce/ubuntu/#install-using-the-convenience-script).

Caso esteja usando um firewall, talvez seja necessário permitir conexões para o endereço IP onde o bundle liquid está rodando. Para *ufw*, isso pode ser feito com:
`sudo ufw allow to 10.66.60.1`.

Clone este repositório e então:

```bash
cd /opt/cluster

sudo apt install -y nohang  # no CentOS, você pode usar "yum"
sudo cp ./examples/nohang.conf /etc/nohang/nohang.conf
sudo systemctl enable nohang.service
sudo systemctl start nohang.service
sudo modprobe br_netfilter || true  # às vezes necessário no Debian

cp examples/cluster.ini .
./bin/docker.sh
docker exec cluster ./cluster.py supervisorctl -- tail -f start
```

Aguarde um minuto e visite:

* [http://10.66.60.1:8500](http://10.66.60.1:8500) - Consul
* [http://10.66.60.1:4646](http://10.66.60.1:4646) - Nomad
* [http://10.66.60.1:8200](http://10.66.60.1:8200) - Vault

Em caso de pânico ou apenas para parar tudo, `docker stop cluster` encerrará tudo imediatamente.

## Executando uma versão específica

Para executar uma versão marcada (ex.: `v0.9.0`) do *cluster*:

```shell
git checkout v0.9.0
./bin/docker.sh --image liquidinvestigations/cluster:0.9.0
```

## Executando um comando dentro de um container de tarefa

Use `cluster.py nomad-exec JOB:TASK COMMAND...` para executar um comando dentro de um container.
`stdin` e `stdout` podem ser usados para troca de dados.
O comando usa
[`nomad alloc exec`](https://nomadproject.io/docs/commands/alloc/exec/).

## Instalando o node

Como próximo passo, você pode instalar o `node` seguindo estas
[instruções](https://github.com/liquidinvestigations/node#installation).

## Mais documentação

* Instalação como container Docker:
  [docs/Docker-Installation.md](docs/Docker-Installation.md)

* Instalação manual no sistema:
  [docs/Manual-Installation.md](docs/Manual-Installation.md)

* Configuração do Vault:
  [docs/Vault.md](docs/Vault.md)

* Execução de um cluster multi-host:
  [docs/Multi-Host.md](docs/Multi-Host.md)

---
