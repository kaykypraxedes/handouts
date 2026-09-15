```
______               _                
|  _  \             | |               
| | | |  ___    ___ | | __  ___  _ __ 
| | | | / _ \  / __|| |/ / / _ \| '__|
| |/ / | (_) || (__ |   < |  __/| |   
|___/   \___/  \___||_|\_\ \___||_|   
```

# 00. Conceitos Básicos

## Docker:

O **Docker** é uma plataforma para construir, distribuir e executar aplicações em ambientes isolados chamados **containers**. A aplicação é empacotada com suas dependências e configurações básicas, reduzindo diferenças entre os ambientes de desenvolvimento, teste e execução.

Um container não é uma máquina virtual completa. Em Linux, os processos dos containers compartilham o kernel do *host*, mas possuem uma visão isolada de recursos como processos, rede, sistema de arquivos e usuários. Essa separação utiliza mecanismos do kernel, principalmente *namespaces* e *cgroups*, fazendo com que containers geralmente sejam mais leves que máquinas virtuais tradicionais.

O isolamento não é absoluto. Containers continuam utilizando o kernel do *host*, e opções como `--privileged`, montagem de diretórios sensíveis e acesso ao socket do Docker podem conceder elevado controle sobre o sistema.

## Arquitetura:

O Docker Engine utiliza uma arquitetura cliente-servidor. O **cliente** `docker` interpreta os comandos e envia solicitações pela API do Docker. O **daemon** `dockerd` recebe essas solicitações e gerencia objetos como imagens, containers, redes e volumes. Cliente e daemon podem estar na mesma máquina ou em máquinas diferentes.

Em uma instalação tradicional no Linux, cliente e daemon normalmente se comunicam pelo socket Unix `/var/run/docker.sock`. Portanto, executar `docker container run` não significa que a própria CLI criou o container: ela solicitou a operação ao daemon.

Um **registry** armazena e distribui imagens. O Docker Hub é o registry utilizado por padrão quando a referência da imagem não informa outro servidor, mas também podem ser usados registries privados ou de outros provedores.

## Imagens e containers:

Uma **imagem** é um modelo imutável, normalmente formado por camadas, que contém o sistema de arquivos e as configurações necessárias para iniciar uma aplicação. Uma mesma imagem pode originar vários containers independentes.

Um **container** é uma instância executável de uma imagem. Ao criá-lo, o Docker acrescenta uma camada gravável e associa configurações como comando principal, variáveis de ambiente, volumes, redes e portas. Alterar ou remover um container não modifica a imagem da qual ele foi criado.

O container permanece em execução enquanto seu processo principal estiver ativo. Esse processo ocupa o PID 1 dentro do container e recebe sinais de parada enviados pelo Docker. Quando ele termina, o container passa para o estado parado, mesmo que sua camada gravável continue existindo.

Por padrão, dados gravados apenas na camada do container pertencem ao seu ciclo de vida. Remover o container também remove essa camada. Dados que precisam sobreviver à substituição do container devem ser armazenados em volumes ou em diretórios montados do *host*.

## Referências de imagens:

Uma imagem pode ser identificada aproximadamente por `{registry}/{namespace}/{repositório}:{tag}` ou por um *digest*. Partes omitidas recebem valores padrão. Por exemplo, `ubuntu:24.04` utiliza o Docker Hub, enquanto `registry.exemplo.com/equipe/app:1.0` informa explicitamente o servidor.

Quando a tag é omitida, o Docker utiliza `latest`. Esse nome é apenas uma tag convencional e mutável: ele não garante que a imagem seja a versão cronologicamente mais recente nem que continue apontando para o mesmo conteúdo no futuro. Um *digest*, como `imagem@sha256:{valor}`, identifica um conteúdo específico.

## Estrutura dos comandos:

A forma canônica da CLI segue geralmente o modelo `docker {objeto} {comando} {opções} {argumentos}`. Alguns comandos também possuem aliases históricos mais curtos. Por exemplo, `docker image ls` pode ser escrito como `docker images`, `docker container ls` como `docker ps`, `docker image rm` como `docker rmi` e `docker container rm` como `docker rm`.

As duas formas são válidas. Neste resumo, a forma organizada por objeto é usada como principal; os aliases ficam reunidos nesta observação para não duplicar cada comando.

## Pesquisa e informações:

Como a CLI possui muitas opções, o resumo apresenta apenas as mais importantes. A documentação integrada e as referências oficiais devem ser consultadas para casos específicos.

- `docker --help` - Mostra os principais comandos e opções globais.

- `docker {objeto} --help` - Mostra as operações disponíveis para um objeto, como `docker image --help`.

- `docker {objeto} {comando} --help` - Mostra a sintaxe, as opções e exemplos de uma operação, como `docker container run --help`.

- `docker compose --help` - Mostra os comandos e opções globais do Docker Compose.

- `docker compose {comando} --help` - Mostra a documentação de uma operação específica do Compose.

- `docker version {opções}` - Mostra as versões e informações do cliente e do servidor.

- `docker info {opções}` - Mostra a configuração e o estado geral do Docker Engine.

Também podem ser consultadas a [Docker CLI Reference](https://docs.docker.com/reference/cli/docker/), a [Dockerfile Reference](https://docs.docker.com/reference/dockerfile/), a [Docker Compose CLI Reference](https://docs.docker.com/reference/cli/docker/compose/) e a [Compose File Reference](https://docs.docker.com/reference/compose-file/).

---

# 01. Configuração e Permissões

## Serviço do Docker:

Em distribuições que utilizam `systemd`, o Docker Engine é executado como um serviço. Dependendo da distribuição e da forma de instalação, pode ser necessário iniciá-lo ou habilitá-lo durante a inicialização do sistema.

- `systemctl {ação} docker.service` - Gerencia o serviço do Docker. Ações importantes: `start`, `stop`, `restart`, `status`, `enable` e `disable`.

- `journalctl {opções} -u docker.service` - Consulta os registros do serviço. Opções importantes: `-f` acompanha novas mensagens, `-b` limita à inicialização atual e `--since` define o período.

O gerenciamento do serviço continua sendo uma operação administrativa e normalmente exige `sudo`, mesmo depois que o usuário recebe acesso à CLI do Docker.

## Execução sem `sudo`:

Na configuração tradicional (*rootful*), o daemon executa como `root` e cria um socket acessível por `root` e pelo grupo `docker`. A necessidade de `sudo` decorre da permissão sobre esse socket, não simplesmente do fato de imagens e containers normalmente serem armazenados em `/var/lib/docker`. O diretório de dados pode, inclusive, ser alterado pela configuração do daemon.

Para permitir que o usuário atual acesse o daemon sem `sudo`:

```bash
$ sudo groupadd docker              # necessário apenas se o grupo ainda não existir
$ sudo usermod -aG docker "$USER"
```

A nova associação de grupo só é aplicada a novos processos. O procedimento recomendado é encerrar completamente a sessão do usuário e entrar novamente. Como alternativa imediata, `newgrp docker` inicia um novo shell usando `docker` como grupo principal.

O acesso pode ser verificado com:

```bash
$ docker container run --rm hello-world
```

Se comandos anteriores foram executados com `sudo`, o diretório `~/.docker` pode ter ficado pertencendo a `root`, causando erros ao carregar a configuração do cliente. Nesse caso, sua propriedade pode ser corrigida com:

```bash
$ sudo chown -R "$USER":"$(id -gn)" "$HOME/.docker"
```

> O grupo `docker` concede privilégios equivalentes aos de `root`. Um usuário capaz de controlar o daemon pode montar diretórios do *host*, iniciar containers privilegiados e modificar o sistema. Portanto, apenas usuários confiáveis devem pertencer a esse grupo.

## Rootless Mode:

No **Rootless Mode**, tanto o daemon quanto os containers são executados por um usuário sem privilégios administrativos dentro de um *user namespace*. Esse modelo reduz o impacto de vulnerabilidades no daemon ou no runtime e evita conceder acesso equivalente a `root` por meio do grupo `docker`.

O modo *rootless* possui instalação, socket, contexto e diretório de dados próprios, além de requisitos de mapeamento de UIDs e GIDs. Por isso, deve ser tratado como uma configuração alternativa completa, e não combinado informalmente com o daemon tradicional.

---

# 02. Imagens e Registries

## Informações:

- `docker image ls {opções} [{repositório}[:{tag}]]` - Lista imagens armazenadas localmente. Opções importantes: `-a` inclui imagens intermediárias, `-q` mostra apenas identificadores, `--filter` filtra e `--format` personaliza a saída.

- `docker image inspect {opções} {imagens}` - Mostra informações detalhadas em JSON, como identificadores, tags, camadas, arquitetura e configuração. A opção `--format` extrai campos específicos.

- `docker image history {opções} {imagem}` - Mostra as camadas e os comandos que formaram uma imagem. Opções importantes: `--no-trunc`, `--human` e `--format`.

## Transferência:

- `docker image pull {opções} {nome}[:{tag}|@{digest}]` - Baixa uma imagem de um registry. Opções importantes: `-a` baixa todas as tags e `--platform` escolhe a plataforma.

- `docker image tag {imagem_origem} {imagem_destino}` - Cria outra referência para a mesma imagem, normalmente antes de enviá-la a um registry.

- `docker image push {opções} {nome}[:{tag}]` - Envia uma imagem para o registry indicado por seu nome. Opções importantes: `-a` envia todas as tags e `--platform` escolhe uma plataforma específica.

- `docker login {opções} [{registry}]` - Autentica o cliente em um registry. Opções importantes: `-u` informa o usuário e `--password-stdin` recebe a credencial pela entrada padrão sem colocá-la diretamente no histórico do shell.

- `docker logout [{registry}]` - Remove do cliente a autenticação armazenada para o registry indicado.

O usuário precisa possuir permissão no namespace de destino antes de executar `push`. As credenciais do cliente normalmente ficam associadas à configuração em `~/.docker/config.json`; quando possível, deve-se utilizar um *credential store* do sistema em vez de manter credenciais codificadas diretamente nesse arquivo.

## Arquivos de imagem:

- `docker image save {opções} {imagens}` - Salva uma ou mais imagens, incluindo camadas e referências, em um arquivo TAR. A opção `-o {arquivo}` define o arquivo de saída.

- `docker image load {opções}` - Carrega imagens e tags de um arquivo TAR criado por `save`. A opção `-i {arquivo}` define o arquivo de entrada; sem ela, o comando lê a entrada padrão.

Exemplo:

```bash
$ docker image save -o imagem.tar aplicacao:1.0
$ docker image load -i imagem.tar
```

`save` e `load` operam sobre imagens. Já `docker container export` e `docker image import` transferem somente uma representação do sistema de arquivos do container, sem preservar da mesma maneira o histórico, as camadas e toda a configuração da imagem.

## Remoção:

- `docker image rm {opções} {imagens}` - Remove referências de imagens locais. A opção `-f` força a operação e `--no-prune` impede a remoção de camadas-pai sem tags.

Uma imagem pode possuir várias tags ou compartilhar camadas com outras imagens. Portanto, remover uma referência não garante que todas as camadas correspondentes sejam imediatamente liberadas. Containers existentes também podem impedir ou alterar o efeito da remoção.

---

# 03. Containers

## Criação e execução:

- `docker container create {opções} {imagem} [{comando} {argumentos}]` - Cria um container sem iniciá-lo.

- `docker container run {opções} {imagem} [{comando} {argumentos}]` - Baixa a imagem se necessário, cria um novo container e inicia seu processo principal.

Algumas das opções mais importantes de `run` são:

- `-d` ou `--detach` - Executa em segundo plano.
- `-i` ou `--interactive` - Mantém a entrada padrão aberta.
- `-t` ou `--tty` - Cria um pseudo-terminal; é frequentemente combinada com `-i` como `-it`.
- `--name {nome}` - Atribui um nome ao container.
- `--rm` - Remove automaticamente o container quando ele termina.
- `-p {host}:{container}` - Publica uma porta do container no *host*.
- `-e {chave}={valor}` - Define uma variável de ambiente.
- `--env-file {arquivo}` - Carrega variáveis de ambiente de um arquivo.
- `--mount {configuração}` - Associa um volume, bind mount ou `tmpfs`.
- `--network {rede}` - Conecta o container a uma rede.
- `--restart {política}` - Define uma política de reinicialização, como `no`, `on-failure`, `always` ou `unless-stopped`.
- `--memory {limite}` e `--cpus {limite}` - Limitam memória e CPU.
- `-u {usuário}[:{grupo}]` - Define o usuário do processo principal.
- `--pull={política}` - Controla a obtenção da imagem: `missing`, `always` ou `never`.

Exemplo:

```bash
$ docker container run -d --name servidor \
    -p 127.0.0.1:8080:80 \
    --restart unless-stopped \
    nginx:alpine
```

Executar `run` novamente cria outro container, mesmo que seja usada a mesma imagem. Para reiniciar um container parado preservando sua configuração e camada gravável, utiliza-se `start`.

## Listagem e informações:

- `docker container ls {opções}` - Lista containers. Sem opções, mostra apenas os que estão em execução; `-a` inclui os parados, `-q` mostra somente identificadores, `--filter` filtra e `--format` personaliza a saída.

- `docker container inspect {opções} {containers}` - Mostra em JSON a configuração e o estado de um ou mais containers. A opção `--format` permite selecionar campos.

- `docker container stats {opções} [containers]` - Mostra o consumo de CPU, memória, rede e E/S. A opção `--no-stream` produz apenas uma leitura.

- `docker container top {container} {opções_do_ps}` - Mostra os processos do container.

- `docker container port {container} [{porta}]` - Mostra as portas publicadas.

## Ciclo de vida:

- `docker container start {opções} {containers}` - Inicia containers parados. Opções importantes: `-a` anexa a saída e `-i` anexa a entrada.

- `docker container stop {opções} {containers}` - Solicita o encerramento gracioso e, após o tempo-limite, força a finalização. Opções importantes: `-s` escolhe o sinal inicial e `-t` define o tempo de espera.

- `docker container kill {opções} {containers}` - Envia imediatamente um sinal; o padrão é `SIGKILL`. A opção `-s` permite escolher outro sinal.

- `docker container restart {opções} {containers}` - Para e inicia novamente os containers. Opções importantes: `-s` e `-t` controlam a parada.

- `docker container rm {opções} {containers}` - Remove containers. Opções importantes: `-f` força a remoção de um container em execução e `-v` remove volumes anônimos associados.

Parar um container não o remove. Sua configuração e sua camada gravável permanecem disponíveis para `start`. A remoção elimina essa camada, mas não elimina automaticamente a imagem nem volumes nomeados.

## Logs e execução de comandos:

- `docker container logs {opções} {container}` - Mostra a saída capturada do processo do container conforme o *logging driver*. Opções importantes: `-f` acompanha novas mensagens, `--tail` limita as últimas linhas, `--since` e `--until` definem o período e `-t` mostra horários.

- `docker container exec {opções} {container} {comando} {argumentos}` - Inicia um novo processo dentro de um container em execução. Opções importantes: `-it` cria uma sessão interativa, `-u` define o usuário, `-e` adiciona variáveis e `-w` define o diretório de trabalho.

Uma sessão interativa pode ser iniciada com:

```bash
$ docker container exec -it meu_container sh
```

`bash` pode ser utilizado quando estiver instalado na imagem. O comando não “entra” literalmente no container: ele cria um novo processo enquanto o processo principal do container permanece ativo. Executar `exit` encerra apenas esse shell iniciado por `exec` e normalmente não para o container.

- `docker container cp {origem} {destino}` - Copia arquivos entre o *host* e um container, usando `{container}:{caminho}` em um dos lados.

---

# 04. Dockerfile e Construção de Imagens

## Dockerfile:

Um **Dockerfile** é um arquivo de texto que descreve de forma reproduzível como construir uma imagem. Suas instruções são processadas em ordem e geralmente produzem camadas reutilizáveis. Quando uma instrução e suas dependências não mudam, o construtor pode reutilizar o *cache* da camada correspondente.

As principais instruções são:

- `FROM {imagem}` - Define a imagem-base e inicia um estágio de construção.
- `WORKDIR {caminho}` - Define o diretório usado pelas instruções seguintes e pelo processo padrão.
- `COPY {origem} {destino}` - Copia arquivos do contexto de construção para a imagem.
- `RUN {comando}` - Executa uma operação durante a construção da imagem.
- `ENV {chave}={valor}` - Define uma variável persistente na configuração da imagem.
- `USER {usuário}` - Define o usuário das instruções seguintes e do processo padrão.
- `EXPOSE {porta}` - Documenta a porta esperada pela aplicação; não a publica no *host*.
- `ENTRYPOINT [...]` - Define o executável principal.
- `CMD [...]` - Define o comando ou os argumentos padrão, que podem ser substituídos na execução.

Exemplo:

```dockerfile
FROM python:3.13-slim

WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY . .

EXPOSE 8000
CMD ["python", "app.py"]
```

A forma JSON de `CMD` e `ENTRYPOINT`, como `CMD ["programa", "argumento"]`, executa diretamente o programa e facilita o tratamento correto de sinais pelo processo principal.

## Contexto e `.dockerignore`:

O **contexto de construção** é o conjunto de arquivos disponibilizado ao construtor. Em `docker image build .`, o ponto indica o diretório atual como contexto. O Dockerfile não consegue copiar arbitrariamente arquivos localizados fora dele.

O arquivo `.dockerignore` exclui itens do contexto, reduzindo transferências, invalidizações de *cache* e o risco de incorporar dados indevidos à imagem:

```gitignore
.git/
.env
node_modules/
build/
*.log
```

Segredos não devem ser gravados em `ENV`, `ARG`, `COPY` ou camadas intermediárias, pois podem permanecer na imagem ou em seu histórico. Para construções que precisam acessar uma credencial, deve-se utilizar o mecanismo de segredos do BuildKit.

## Construção:

- `docker image build {opções} {contexto}` - Constrói uma imagem a partir de um Dockerfile. Opções importantes: `-t {nome}:{tag}` atribui uma referência, `-f {arquivo}` escolhe outro Dockerfile, `--no-cache` ignora o *cache*, `--pull` tenta atualizar a imagem-base e `--build-arg` fornece argumentos de construção.

Exemplo:

```bash
$ docker image build -t minha_aplicacao:1.0 .
```

## Captura de um container:

- `docker container commit {opções} {container} [{repositório}[:{tag}]]` - Cria uma imagem a partir das alterações presentes na camada gravável de um container.

`commit` pode ser útil para investigação ou captura pontual, mas não substitui um Dockerfile: a sequência que produziu o estado não fica documentada nem pode ser reproduzida com segurança. Alterações armazenadas em volumes montados não são incluídas na imagem criada.

---

# 05. Armazenamento

## Tipos de armazenamento:

O Docker oferece três formas principais de disponibilizar armazenamento adicional a um container:

- **Volume:** área persistente gerenciada pelo Docker e independente do ciclo de vida do container. É normalmente a opção preferida para bancos de dados e dados de aplicações.
- **Bind mount:** arquivo ou diretório existente no *host* montado diretamente no container. É útil para código-fonte e configurações, mas acopla o container à estrutura do *host*.
- **`tmpfs`:** armazenamento temporário mantido na memória do *host* e removido quando o container para.

## Volumes:

- `docker volume create {opções} [{volume}]` - Cria um volume. Opções importantes: `--driver`, `--label` e `--opt`.

- `docker volume ls {opções}` - Lista volumes. Opções importantes: `-q`, `--filter` e `--format`.

- `docker volume inspect {opções} {volumes}` - Mostra detalhes dos volumes. A opção `--format` seleciona campos.

- `docker volume rm {opções} {volumes}` - Remove volumes não utilizados. A opção `-f` força a solicitação, mas volumes em uso por containers não podem ser removidos.

- `docker volume prune {opções}` - Remove, por padrão, volumes anônimos locais não utilizados. A opção `-a` inclui volumes nomeados não utilizados, `--filter` limita o alcance e `-f` dispensa confirmação.

Um volume pode ser associado durante a criação do container com:

```bash
$ docker container run --mount \
    type=volume,src=dados,dst=/var/lib/aplicacao \
    imagem:tag
```

## Bind mounts:

Um bind mount pode ser criado com:

```bash
$ docker container run --mount \
    type=bind,src="$PWD/config",dst=/app/config,readonly \
    imagem:tag
```

O modo somente leitura reduz o risco de o container alterar o *host*. Ainda assim, processos com acesso à montagem podem ler os arquivos nela presentes; diretórios contendo chaves, credenciais ou dados pessoais não devem ser montados sem necessidade.

A opção curta `-v` ou `--volume` aceita o formato `{origem}:{destino}:{opções}`. Em sistemas com SELinux, como Fedora, `:Z` cria um rótulo privado para um container e `:z` um rótulo compartilhável entre containers:

```bash
$ docker container run -v "$PWD/dados:/app/dados:Z" imagem:tag
```

O relabeling modifica os rótulos SELinux dos arquivos do *host* e deve ser aplicado apenas ao diretório necessário.

---

# 06. Redes e Portas

## Redes:

Por padrão, containers recebem uma interface virtual e são conectados a uma rede. A rede `bridge` padrão fornece conectividade básica, mas redes *bridge* criadas pelo usuário oferecem melhor isolamento e resolução de nomes entre containers.

Containers conectados à mesma rede personalizada podem normalmente utilizar seus nomes como *hostnames*, evitando depender de endereços IP que podem mudar.

- `docker network create {opções} {rede}` - Cria uma rede. Opções importantes: `-d` escolhe o driver, `--subnet` define a sub-rede e `--internal` restringe acesso externo.

- `docker network ls {opções}` - Lista redes. Opções importantes: `-q`, `--filter` e `--format`.

- `docker network inspect {opções} {redes}` - Mostra configuração e containers conectados.

- `docker network connect {opções} {rede} {container}` - Conecta um container existente a uma rede.

- `docker network disconnect {opções} {rede} {container}` - Desconecta um container.

- `docker network rm {redes}` - Remove redes que não estejam em uso.

## Publicação de portas:

Publicar uma porta cria um encaminhamento do *host* para o container. A forma geral é `{IP_do_host}:{porta_do_host}:{porta_do_container}/{protocolo}`.

- `-p 8080:80` - Publica a porta TCP 80 do container na porta 8080 das interfaces do *host*.
- `-p 127.0.0.1:8080:80` - Limita o acesso à própria máquina.
- `-p 5353:53/udp` - Publica uma porta utilizando UDP.

Se o endereço do *host* for omitido, a porta normalmente é vinculada a todas as interfaces, podendo ficar acessível por outras máquinas. Para serviços destinados somente ao desenvolvimento local, convém declarar explicitamente `127.0.0.1`.

`EXPOSE` no Dockerfile documenta a porta usada pela aplicação, mas não cria essa publicação. A opção `-p` ou uma declaração equivalente do Compose é necessária para acessá-la através do *host*.

---

# 07. Docker Compose

## Compose:

O **Docker Compose** é uma ferramenta declarativa para definir e executar aplicações formadas por um ou mais containers. Em vez de manter diversos comandos `docker container run`, `docker network create` e `docker volume create`, a configuração da aplicação é registrada em um arquivo YAML e aplicada como um conjunto.

Compose não substitui o Docker Engine. Ele funciona como outro cliente da API e solicita ao mesmo daemon a criação de imagens, containers, redes e volumes. Também não é, por si só, um orquestrador de cluster equivalente a Kubernetes ou Docker Swarm; seu foco principal é gerenciar uma aplicação em um Engine ou contexto Docker.

As responsabilidades podem ser resumidas da seguinte maneira:

| Elemento | Responsabilidade |
| --- | --- |
| Dockerfile | Descrever como construir uma imagem. |
| `docker container run` | Criar e configurar imperativamente um container. |
| `compose.yaml` | Declarar os serviços, redes e volumes de uma aplicação. |
| Docker Engine | Construir e executar efetivamente os objetos solicitados. |

## Instalação e sintaxe:

As instalações atuais utilizam o plugin acessado por `docker compose`. O comando separado `docker-compose`, com hífen, corresponde à instalação *standalone* mantida principalmente por compatibilidade e não é a forma recomendada para novas configurações.

- `docker compose version {opções}` - Mostra a versão instalada do Compose.

No Linux, o plugin normalmente é disponibilizado pelo pacote `docker-compose-plugin` dos repositórios oficiais do Docker. O Docker Desktop já inclui o Compose.

## Arquivo Compose:

O nome preferencial é `compose.yaml`. Também são reconhecidos `compose.yml`, `docker-compose.yaml` e `docker-compose.yml`, estes últimos por compatibilidade. A antiga propriedade superior `version` não é necessária nas versões atuais, que seguem a **Compose Specification**.

Um **projeto Compose** agrupa os recursos de uma aplicação. Seu nome pode ser definido por `name`, pela opção global `-p` ou ser derivado do diretório, sendo usado como prefixo e rótulo dos containers, redes e volumes criados.

Os principais elementos do arquivo são:

- `services` - Define os componentes executáveis da aplicação.
- `image` - Seleciona uma imagem existente.
- `build` - Define o contexto e as opções para construir uma imagem.
- `command` e `entrypoint` - Sobrescrevem o comando ou ponto de entrada da imagem.
- `ports` - Publica portas no *host*.
- `volumes` - Associa volumes ou bind mounts.
- `environment` - Define variáveis no ambiente do container.
- `env_file` - Carrega variáveis que serão entregues ao container.
- `depends_on` - Declara dependências e controla a ordem de inicialização.
- `healthcheck` - Define como verificar a saúde ou prontidão do serviço.
- `networks` - Conecta serviços a redes específicas.
- `restart` - Define a política de reinicialização.

Exemplo:

```yaml
name: exemplo

services:
  aplicacao:
    build: .
    ports:
      - "127.0.0.1:${APP_PORT:-8000}:8000"
    environment:
      REDIS_HOST: cache
    depends_on:
      cache:
        condition: service_healthy
    restart: unless-stopped

  cache:
    image: redis:alpine
    volumes:
      - dados_cache:/data
    healthcheck:
      test: ["CMD", "redis-cli", "ping"]
      interval: 5s
      timeout: 3s
      retries: 5

volumes:
  dados_cache:
```

Compose cria uma rede padrão para o projeto. No exemplo, `aplicacao` pode acessar o serviço `cache` usando `cache` como *hostname*. O volume `dados_cache` é declarado no nível superior e permanece disponível quando os containers são substituídos.

`depends_on` em sua forma simples controla a ordem de início, mas não garante que o programa dependente esteja pronto para aceitar conexões. A combinação de `healthcheck` e `condition: service_healthy` permite aguardar o estado saudável declarado.

## Variáveis:

Compose pode substituir expressões como `${VARIAVEL}` com valores do ambiente do shell ou de um arquivo `.env`. Também são aceitas formas como `${VARIAVEL:-padrão}` e `${VARIAVEL:?mensagem}`, que respectivamente definem um padrão e exigem um valor.

O `.env` é usado principalmente para **interpolar o modelo Compose**. Suas variáveis não são automaticamente entregues a todos os containers: para isso, precisam ser declaradas em `environment` ou carregadas em `env_file`.

```dotenv
APP_PORT=8000
```

Arquivos `.env` e `env_file` são arquivos de texto, não cofres de segredos. Eles devem receber permissões adequadas e não devem ser versionados quando contiverem credenciais. A substituição pode ser conferida com `docker compose config`.

## Opções globais:

A forma geral é `docker compose {opções} {comando} {argumentos}`. Algumas opções globais importantes são:

- `-f {arquivo}` - Escolhe um arquivo Compose; pode ser repetida para combinar arquivos.
- `-p {projeto}` - Define o nome do projeto.
- `--env-file {arquivo}` - Escolhe o arquivo usado na interpolação.
- `--profile {perfil}` - Ativa um perfil opcional.

As opções globais devem aparecer antes do subcomando, como em `docker compose -f compose.dev.yaml up`.

## Criação e atualização:

- `docker compose up {opções} [serviços]` - Constrói quando necessário, cria ou recria e inicia os serviços. Sem `-d`, agrega os logs e mantém o terminal anexado. Opções importantes: `-d`, `--build`, `--pull`, `--force-recreate`, `--no-recreate`, `--remove-orphans`, `--scale` e `--wait`.

- `docker compose create {opções} [serviços]` - Cria os containers sem iniciá-los.

- `docker compose build {opções} [serviços]` - Constrói as imagens dos serviços que utilizam `build`. Opções importantes: `--no-cache`, `--pull` e `--build-arg`.

- `docker compose pull {opções} [serviços]` - Baixa as imagens declaradas. Opções importantes: `--ignore-buildable`, `--include-deps` e `--policy`.

Quando a configuração ou a imagem de um serviço muda, `up` pode substituir seu container preservando os volumes montados. Modificações feitas apenas na camada gravável do container antigo podem ser perdidas; a aplicação deve ser reconstruível pelas imagens, configurações e volumes declarados.

## Informações e comandos:

- `docker compose config {opções} [serviços]` - Valida, combina e mostra o modelo resolvido. Opções importantes: `--environment`, `--images`, `--services`, `--volumes` e `-q`.

- `docker compose ps {opções} [serviços]` - Lista os containers do projeto. Opções importantes: `-a`, `-q`, `--filter`, `--format` e `--status`.

- `docker compose logs {opções} [serviços]` - Mostra os logs dos serviços. Opções importantes: `-f`, `--tail`, `--since`, `--until` e `-t`.

- `docker compose exec {opções} {serviço} {comando} {argumentos}` - Executa um processo em um container ativo do serviço. Opções importantes: `-u`, `-e`, `-w` e `-T`, que desativa o pseudo-terminal alocado por padrão.

- `docker compose run {opções} {serviço} {comando} {argumentos}` - Cria um container separado para uma tarefa pontual baseada no serviço. Opções importantes: `--rm`, `--no-deps`, `-e`, `-v` e `--entrypoint`.

`exec` utiliza um container que já está executando. `run`, por outro lado, cria um container temporário baseado na configuração do serviço, sendo útil para testes, migrações e tarefas administrativas.

## Parada e remoção:

- `docker compose stop {opções} [serviços]` - Para containers sem removê-los. A opção `-t` define o tempo de espera.

- `docker compose start [serviços]` - Inicia containers existentes; não cria os que ainda não existem.

- `docker compose restart {opções} [serviços]` - Reinicia containers. A opção `-t` define o tempo de espera.

- `docker compose down {opções} [serviços]` - Para e remove containers e redes criados para o projeto. Opções importantes: `-v` remove volumes nomeados declarados e anônimos associados, `--rmi` remove imagens e `--remove-orphans` remove containers de serviços que não estão mais no arquivo.

Por padrão, `down` não remove volumes nomeados nem recursos declarados como externos. `docker compose down -v` remove os volumes do projeto e pode apagar permanentemente bancos de dados e outros dados persistentes.

---

# 08. Manutenção e Segurança

## Uso de espaço:

- `docker system df {opções}` - Mostra o espaço utilizado por imagens, containers, volumes locais e *build cache*. A opção `-v` apresenta detalhes.

- `docker system prune {opções}` - Remove recursos não utilizados, como containers parados, redes sem uso, imagens pendentes e *build cache*. Opções importantes: `-a` amplia a remoção para imagens não utilizadas por containers, `--volumes` inclui volumes anônimos, `--filter` restringe e `-f` dispensa confirmação.

Comandos `prune` determinam o alcance com base nos recursos atualmente referenciados, não na importância de seus dados. A lista apresentada e as opções devem ser conferidas antes da confirmação, especialmente ao incluir volumes.

## Eventos:

- `docker system events {opções}` - Acompanha eventos produzidos pelo daemon, como criação, início, parada e remoção de containers. Opções importantes: `--since`, `--until`, `--filter` e `--format`.

Esse comando é diferente de `docker container logs`: `events` mostra acontecimentos do daemon, enquanto `logs` recupera a saída do processo executado no container.

## Cuidados de segurança:

- `--privileged` concede ao container acesso muito amplo a dispositivos e recursos do *host* e não deve ser usado como solução genérica para erros de permissão.
- Montar `/var/run/docker.sock` dentro de um container concede a ele controle sobre o daemon e, na configuração tradicional, privilégios equivalentes aos de `root` no *host*.
- Bind mounts permitem que o container leia ou modifique caminhos reais do *host*; devem ser limitados ao necessário e, quando possível, montados como somente leitura.
- Imagens, Dockerfiles e arquivos Compose de terceiros devem ser examinados antes da execução, pois podem solicitar montagens, dispositivos, redes e privilégios perigosos.
- Tags de imagens são mutáveis. Para ambientes que exigem repetibilidade rigorosa, versões devem ser fixadas e, quando necessário, validadas por *digest*.

---

# Fontes:

- DOCKER INC. *Docker Docs: Docker overview*. [S. l.]: Docker Inc., [s. d.]. Disponível em: <https://docs.docker.com/get-started/docker-overview/>. Acesso em: 15 set. 2026.

- DOCKER INC. *Docker Engine: Linux post-installation steps*. [S. l.]: Docker Inc., [s. d.]. Disponível em: <https://docs.docker.com/engine/install/linux-postinstall/>. Acesso em: 15 set. 2026.

- DOCKER INC. *Docker Engine security: Rootless mode*. [S. l.]: Docker Inc., [s. d.]. Disponível em: <https://docs.docker.com/engine/security/rootless/>. Acesso em: 15 set. 2026.

- DOCKER INC. *Docker CLI reference*. [S. l.]: Docker Inc., [s. d.]. Disponível em: <https://docs.docker.com/reference/cli/docker/>. Acesso em: 15 set. 2026.

- DOCKER INC. *Dockerfile reference*. [S. l.]: Docker Inc., [s. d.]. Disponível em: <https://docs.docker.com/reference/dockerfile/>. Acesso em: 15 set. 2026.

- DOCKER INC. *Storage*. [S. l.]: Docker Inc., [s. d.]. Disponível em: <https://docs.docker.com/engine/storage/>. Acesso em: 15 set. 2026.

- DOCKER INC. *Networking overview*. [S. l.]: Docker Inc., [s. d.]. Disponível em: <https://docs.docker.com/engine/network/>. Acesso em: 15 set. 2026.

- DOCKER INC. *Docker Compose*. [S. l.]: Docker Inc., [s. d.]. Disponível em: <https://docs.docker.com/compose/>. Acesso em: 15 set. 2026.

- DOCKER INC. *How Compose works*. [S. l.]: Docker Inc., [s. d.]. Disponível em: <https://docs.docker.com/compose/intro/compose-application-model/>. Acesso em: 15 set. 2026.

- DOCKER INC. *Compose file reference*. [S. l.]: Docker Inc., [s. d.]. Disponível em: <https://docs.docker.com/reference/compose-file/>. Acesso em: 15 set. 2026.

- DOCKER INC. *Set, use, and manage variables in a Compose file with interpolation*. [S. l.]: Docker Inc., [s. d.]. Disponível em: <https://docs.docker.com/compose/how-tos/environment-variables/variable-interpolation/>. Acesso em: 15 set. 2026.
