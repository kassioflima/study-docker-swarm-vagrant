# Cluster Docker Swarm local com Vagrant

Projeto do desafio de criação de um cluster Docker Swarm local com quatro máquinas virtuais.

| Máquina | IP privado | Papel no Swarm |
|---|---:|---|
| `master` | `192.168.56.10` | Manager |
| `node01` | `192.168.56.11` | Worker |
| `node02` | `192.168.56.12` | Worker |
| `node03` | `192.168.56.13` | Worker |

## Pré-requisitos

- [VirtualBox](https://www.virtualbox.org/)
- [Vagrant](https://developer.hashicorp.com/vagrant)

## Subir o ambiente

Na raiz deste projeto, execute:

```bash
vagrant up
```

O provisionamento instala o Docker em todas as VMs, inicializa o Swarm no `master` e registra os três workers automaticamente. A primeira execução pode demorar, pois a imagem Ubuntu e os pacotes do Docker serão baixados.

## Validar o cluster

```bash
vagrant ssh master -c "docker node ls"
```

O resultado deve exibir quatro nós, com `master` em estado `Leader` e `node01`, `node02` e `node03` em `Ready`.

Como teste adicional, publique um serviço distribuído:

```bash
vagrant ssh master -c "docker service create --name web --replicas 3 -p 8080:80 nginx:alpine"
vagrant ssh master -c "docker service ls"
vagrant ssh master -c "docker service ps web"
```

Para remover o serviço de teste:

```bash
vagrant ssh master -c "docker service rm web"
```

## Comandos úteis

```bash
vagrant status
vagrant ssh master
vagrant halt
vagrant destroy -f
```

`vagrant provision` pode ser executado novamente sem recriar o cluster: os scripts verificam se o nó já participa de um Swarm antes de inicializar ou entrar novamente.

> O arquivo temporário `.worker_join_token` é criado no diretório sincronizado apenas durante o provisionamento e está ignorado pelo Git.
