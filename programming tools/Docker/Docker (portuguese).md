```
______               _                
|  _  \             | |               
| | | |  ___    ___ | | __  ___  _ __ 
| | | | / _ \  / __|| |/ / / _ \| '__|
| |/ / | (_) || (__ |   < |  __/| |   
|___/   \___/  \___||_|\_\ \___||_|   
```

**Autor: Kayky Moreira Praxedes**

# 0. Conceitos Básicos

## Docker:

O **Docker** permite preparar e executar aplicações com os programas e bibliotecas de que elas precisam, em ambientes isolados chamados ***containers***. Isso reduz diferenças entre computadores: em vez de instalar e configurar cada dependência diretamente no sistema, utiliza-se um ambiente preparado para a aplicação.

O computador que executa esse ambiente é chamado de *host*. Nos exemplos desta apostila, ele é o próprio computador em que os comandos são digitados.

## Imagens e *Containers*:

Uma **imagem** contém os arquivos e as configurações usados como base para executar uma aplicação. Um ***container*** é o ambiente criado a partir dessa imagem, com seus próprios processos, configurações e alterações de arquivos.

Uma imagem pode originar vários *containers*. Alterar os arquivos de um deles não modifica a imagem original nem os arquivos dos demais, exceto quando eles utilizam armazenamento compartilhado.

> Para quem conhece orientação a objetos, uma comparação possível é pensar na imagem como uma classe e no *container* como um objeto criado a partir dela.

Um *container* permanece em execução enquanto seu **processo principal** estiver ativo. Se esse programa terminar, o *container* para. Parar não significa remover: seus arquivos continuam disponíveis para uma nova inicialização. Ao remover o *container*, os dados gravados apenas nele são apagados; para mantê-los independentemente dele, utilizam-se volumes ou diretórios montados (capítulo 5).

*Containers* Linux compartilham o núcleo (*kernel*) do ambiente Linux em que são executados, em vez de cada um iniciar um sistema operacional completo. No Docker Engine instalado diretamente no Linux, esse núcleo é o do hospedeiro; no Docker Desktop, os *containers* Linux normalmente ficam dentro de uma máquina virtual gerenciada pela ferramenta.

## *Registries* e Nomes de Imagens:

Um ***registry*** é um serviço que armazena e distribui imagens. O **Docker Hub** é utilizado por padrão, mas também existem servidores privados e de outros provedores.

A referência `nginx:alpine` identifica a imagem `nginx` e a ***tag*** `alpine`, que seleciona uma versão ou variante. Uma referência como `registry.exemplo.com/equipe/app:1.0` também informa o servidor e a organização do repositório.

Se a *tag* for omitida, o Docker utiliza `latest`. Esse nome é uma convenção: não garante que seja a versão mais recente. *Tags* podem passar a apontar para outro conteúdo quando uma imagem é atualizada.

## Estrutura dos Comandos:

A forma mais utilizada nesta apostila é `docker {objeto} {comando} {opções} {argumentos}`. O objeto indica o recurso, como `image`, `container` ou `volume`; o comando indica a operação, como `ls`, `run` ou `rm`.

> Elementos entre `{chaves}` devem ser substituídos, sem manter as chaves. `{opções}` representa modificadores opcionais; a descrição principal apresenta o comportamento sem eles, considerando os argumentos indicados. Quando um argumento puder ser omitido, isso será explicado no texto. Argumentos no plural, como `{containers}`, aceitam um ou mais nomes ou identificadores separados por espaços.

Alguns comandos possuem formas curtas: `docker container run` equivale a `docker run`, `docker container ls` a `docker ps`, `docker image ls` a `docker images` e `docker image rm` a `docker rmi`. Aqui, a forma extensa é mantida para deixar explícito o recurso utilizado.

## Manuais:

- `docker --help` - Mostra os principais comandos e opções globais.

- `docker {objeto} --help` - Mostra as operações disponíveis para um recurso, como `docker image --help`.

- `docker {objeto} {comando} --help` - Mostra a ajuda de uma operação, como `docker container run --help`.

- `docker compose {comando} --help` - Mostra a ajuda de uma operação do Compose (capítulo 7). Sem o comando, `docker compose --help` mostra a ajuda geral.

---

# 1. Configuração e Permissões

## Instalação e Verificação:

O comando `docker` utiliza um serviço, chamado **Docker Engine**, para gerenciar imagens, *containers*, redes e volumes. Ter o comando instalado não significa que esse serviço esteja em execução.

Consulte as instruções oficiais para instalar o [Docker Engine](https://docs.docker.com/engine/install/) ou o [Docker Desktop](https://docs.docker.com/desktop/). O Desktop reúne o ambiente de execução, uma interface gráfica e o Compose; no Linux, o Engine também pode ser instalado diretamente, sem o Desktop.

- `docker version {opções}` - Mostra as versões do cliente e do servidor. Se o cliente responder, mas a parte do servidor apresentar erro, confira se o serviço está disponível e se seu usuário pode acessá-lo.

- `docker info {opções}` - Mostra informações sobre o ambiente em execução, incluindo quantidade de imagens e *containers*.

## Serviço no Linux:

Os comandos abaixo se aplicam ao **Docker Engine instalado como serviço de sistema em distribuições com `systemd`**. No Docker Desktop, o ambiente é iniciado pelo próprio aplicativo.

- `sudo systemctl start docker` - Inicia o serviço agora.

- `sudo systemctl enable docker` - Configura a inicialização automática do serviço com o sistema.

- `systemctl status docker` - Consulta o estado do serviço.

- `sudo journalctl -u docker.service` - Consulta os registros do serviço. `-f` acompanha novas mensagens e `-b` limita à inicialização atual.

## Executar sem `sudo`:

Na instalação tradicional do Engine no Linux, o acesso depende da permissão para comunicar-se com o serviço. Para permitir o acesso pelo grupo `docker`:

```bash
# Execute apenas se o grupo ainda não existir.
sudo groupadd docker

# Adicione seu usuário ao grupo.
sudo usermod -aG docker "$USER"
```

Depois disso, basta atualizar o terminal.

> Isso não dispensa `sudo` para operações administrativas como iniciar o serviço.

**Participar do grupo `docker` concede acesso equivalente ao de administrador sobre o computador.** Essa configuração deve ser limitada a usuários confiáveis.

> O modo *rootless* é uma alternativa em que o serviço e os *containers* são executados sem privilégios administrativos. Ele possui instalação e configuração próprias; consulte o [guia oficial](https://docs.docker.com/engine/security/rootless/).

---

# 2. Imagens e *Registries*

## Consultar e Baixar Imagens:

- `docker image ls {opções}` - Lista as imagens armazenadas localmente. `-q` mostra apenas os identificadores.

- `docker image inspect {opções} {imagens}` - Mostra informações detalhadas em JSON, como configuração, arquitetura e identificadores. `--format` permite selecionar campos.

- `docker image pull {opções} {imagem}` - Baixa uma imagem do *registry* indicado ou, por padrão, do Docker Hub.

```bash
docker image pull nginx:alpine
docker image ls
```

Baixar uma imagem não inicia a aplicação. Ela fica disponível para criar *containers*.

## Identificar e Publicar Imagens:

- `docker image tag {origem} {destino}` - Cria outro nome ou *tag* para a mesma imagem local, sem duplicar seu conteúdo.

- `docker login {opções} {registry}` - Autentica no servidor indicado. Sem o servidor, utiliza o Docker Hub. O fluxo de autenticação depende do serviço.

    - `docker login -u {usuário} --password-stdin {registry}` - Recebe a senha ou o token pela entrada padrão, evitando colocá-lo diretamente no comando.

- `docker image push {opções} {imagem}` - Envia a imagem ao *registry* e repositório indicados em seu nome. É necessário ter permissão de publicação no destino.

- `docker logout {registry}` - Remove a autenticação local para o servidor indicado; se omitido, utiliza o Docker Hub.

Supondo que `minha_aplicacao:1.0` já exista localmente, a publicação no Docker Hub pode ser feita assim, substituindo `seu_usuario` pela conta autorizada:

```bash
docker image tag minha_aplicacao:1.0 seu_usuario/minha_aplicacao:1.0
docker login
docker image push seu_usuario/minha_aplicacao:1.0
```

As credenciais são associadas à configuração do cliente. Prefira a integração com o cofre de credenciais do sistema; credenciais apenas codificadas em `~/.docker/config.json` não estão criptografadas.

## Remover Imagens:

- `docker image rm {opções} {imagens}` - Remove as referências locais indicadas. Imagens utilizadas por *containers* podem impedir a operação.

    - `docker image rm -f {imagens}` - Força a remoção nas situações permitidas pelo Docker; não substitui o gerenciamento dos *containers* que utilizam a imagem.

Uma imagem pode possuir vários nomes e compartilhar partes de seu conteúdo com outras imagens. Por isso, remover uma referência nem sempre libera todo o espaço mostrado para ela.

## Transferir Imagens por Arquivo:

- `docker image save {opções} {imagens}` - Gera um arquivo TAR com as imagens, enviando o conteúdo para a saída padrão quando nenhum arquivo de destino é escolhido.

    - `docker image save -o {arquivo} {imagens}` - Grava o TAR no arquivo indicado.

- `docker image load {opções}` - Carrega imagens de um TAR recebido pela entrada padrão.

    - `docker image load -i {arquivo}` - Lê o TAR do arquivo indicado.

```bash
docker image save -o nginx.tar nginx:alpine
docker image load -i nginx.tar
```

Esses comandos transferem imagens. Os dados armazenados nos *containers* e volumes não são incluídos automaticamente.

---

# 3. *Containers*

## Criar e Executar:

- `docker container create {opções} {imagem}` - Cria um *container* sem iniciá-lo.

- `docker container run {opções} {imagem} {comando} {argumentos}` - Cria um novo *container* e inicia seu processo principal. Baixa a imagem se ela não estiver disponível localmente. O comando e seus argumentos podem ser omitidos para utilizar o padrão definido pela imagem.

    - `docker container run --name {nome} {imagem}` - Atribui um nome para facilitar os próximos comandos.
    - `docker container run -d {imagem}` - Executa em segundo plano e libera o terminal.
    - `docker container run -it {imagem} {comando}` - Mantém a entrada aberta (`-i`) e disponibiliza um terminal (`-t`), permitindo interação com o programa.
    - `docker container run --rm {imagem}` - Remove automaticamente o *container* quando ele termina.

As opções podem ser combinadas:

```bash
docker container run --name servidor -d nginx:alpine
docker container run --rm -it alpine:3.22 sh
```

O primeiro comando mantém o servidor em segundo plano, ainda sem publicar uma porta para acesso pelo navegador. O segundo abre um terminal `sh` em outro *container*; ao executar `exit`, seu processo principal termina e ele é removido.

**Cada `run` cria um novo container.** Para iniciar novamente um que está parado, utilize `start`. Repetir `run` com o mesmo `--name` gera conflito enquanto o *container* anterior existir, mesmo parado.

## Consultar Estado e Recursos:

- `docker container ls {opções}` - Lista os *containers* em execução. `-q` mostra apenas os identificadores.

    - `docker container ls -a` - Inclui os *containers* parados.

- `docker container inspect {opções} {containers}` - Mostra a configuração e o estado em JSON. `--format` permite selecionar campos.

- `docker container stats {opções} {containers}` - Acompanha o consumo de CPU, memória, rede e leitura/escrita. Sem nomes, acompanha os *containers* em execução; `--no-stream` mostra apenas uma leitura.

- `docker container top {container}` - Mostra os processos do *container*.

## Parar, Retomar e Remover:

- `docker container stop {opções} {containers}` - Solicita o encerramento do processo e força a finalização se ele não parar no prazo. `-t {segundos}` ajusta essa espera.

- `docker container start {opções} {containers}` - Inicia *containers* existentes que estão parados, preservando sua configuração.

    - `docker container start -ai {container}` - Inicia e conecta o terminal à entrada e à saída do *container*.

- `docker container restart {opções} {containers}` - Para e inicia novamente. `-t {segundos}` ajusta a espera durante a parada.

- `docker container kill {opções} {containers}` - Força o encerramento imediato com `SIGKILL`. A opção `-s {sinal}` permite enviar outro sinal.

- `docker container rm {opções} {containers}` - Remove *containers* parados e os dados gravados apenas neles.

    - `docker container rm -f {containers}` - Força o encerramento e a remoção de *containers* em execução.
    - `docker container rm -v {containers}` - Também remove volumes anônimos associados; não remove volumes nomeados.

```bash
docker container stop servidor
docker container start servidor
docker container stop servidor
docker container rm servidor
```

A sequência para, retoma e, por fim, remove o servidor do exemplo anterior. A imagem permanece disponível. Parar preserva os arquivos do *container*; remover elimina sua camada gravável.

## Consultar *Logs* e Executar Comandos:

- `docker container logs {opções} {container}` - Mostra a saída registrada da aplicação. `--tail {quantidade}` limita às últimas linhas e `-t` acrescenta horários. A disponibilidade depende da configuração de registro da aplicação e do Docker.

    - `docker container logs -f {container}` - Acompanha novas mensagens. `Ctrl+C` encerra o acompanhamento, sem parar o *container*.

- `docker container exec {opções} {container} {comando} {argumentos}` - Executa outro processo em um *container* que já está em execução. Os argumentos dependem do programa e podem ser omitidos.

    - `docker container exec -it {container} sh` - Abre uma sessão interativa de `sh`, se esse programa existir no *container*.

É possível utilizar `bash` no lugar de `sh` quando estiver instalado. Nesse caso, `exit` encerra a sessão criada por `exec`, enquanto o processo principal continua funcionando.

- `docker container cp {origem} {destino}` - Copia arquivos entre o computador e um *container*. Utilize `{container}:{caminho}` para indicar o lado do *container*.

```bash
docker container cp pagina.html servidor:/usr/share/nginx/html/index.html
docker container cp servidor:/etc/nginx/nginx.conf ./nginx.conf
```

Os exemplos pressupõem um *container* Nginx chamado `servidor`; a primeira linha também exige o arquivo local `pagina.html`.

## Configurar a Aplicação:

- `docker container run -e {chave}={valor} {imagem}` - Define uma variável de ambiente para a aplicação.

- `docker container run --env-file {arquivo} {imagem}` - Carrega variáveis de um arquivo com linhas no formato `CHAVE=valor`.

- `docker container run --restart {política} {imagem}` - Define quando reiniciar automaticamente o *container*. `no` desativa a política; `on-failure` reage a encerramento com erro; `unless-stopped` permite retomadas automáticas, preservando a decisão de uma parada manual.

Essas configurações são definidas ao criar o *container*. Um `start` reutiliza as existentes. Variáveis só produzem efeito se a aplicação as reconhecer, e não devem ser tratadas como um cofre de credenciais.

> Para necessidades específicas, `--memory` e `--cpus` limitam recursos; `-u` escolhe o usuário do processo; `--pull` controla quando obter a imagem. Consulte `docker container run --help` para a sintaxe e os valores aceitos. Portas, redes e armazenamento são detalhados nos capítulos 5 e 6.

---

# 4. *Dockerfile* e Construção de Imagens

## *Dockerfile*:

Um ***Dockerfile*** é um arquivo de texto com as instruções para construir uma imagem. Ele registra como preparar os arquivos e o ambiente, permitindo repetir a construção sem depender de alterações manuais em um *container*.

- `FROM {imagem}` - Escolhe a imagem-base.

- `WORKDIR {caminho}` - Define o diretório utilizado pelas instruções seguintes e pelo programa executado ao iniciar o *container*.

- `COPY {origem} {destino}` - Copia arquivos do contexto de construção para a imagem.

- `RUN {comando}` - Executa uma operação durante a construção, como instalar dependências.

- `ENV {chave}={valor}` - Define uma variável no ambiente da imagem.

- `USER {usuário}` - Define o usuário para as próximas instruções `RUN` e para a execução do *container*.

- `EXPOSE {porta}` - Documenta a porta utilizada pela aplicação. Não publica a porta no computador.

- `ENTRYPOINT ["{programa}", "{argumento}"]` - Define o programa principal. A lista pode ter apenas o programa ou incluir vários argumentos.

- `CMD ["{programa}", "{argumento}"]` - Define o comando padrão. Quando existe um `ENTRYPOINT`, o conteúdo de `CMD` normalmente fornece seus argumentos padrão.

**`RUN` acontece na construção; `CMD` e `ENTRYPOINT` definem a execução do container.** Ao fornecer outro comando em `docker container run`, ele substitui o `CMD`; com um `ENTRYPOINT` na forma de lista, esse conteúdo é passado como argumento ao programa principal.

## Contexto e `.dockerignore`:

O **contexto de construção** reúne os arquivos disponíveis para criar a imagem. Em `docker image build .`, o ponto indica a pasta atual. Os caminhos de origem de `COPY` são relativos a esse contexto, não a qualquer pasta do computador.

O arquivo `.dockerignore`, colocado na raiz do contexto, exclui arquivos que não precisam ser enviados para a construção:

```gitignore
.git/
.env
node_modules/
build/
*.log
```

Isso reduz o conteúdo transferido e evita incluir arquivos locais desnecessários. Credenciais não devem ser copiadas para a imagem ou gravadas em suas instruções; quando a construção precisar delas, utilize os [segredos de construção](https://docs.docker.com/build/building/secrets/).

## Construir uma Imagem:

- `docker image build {opções} {contexto}` - Constrói uma imagem utilizando, por padrão, o arquivo `Dockerfile` na raiz do contexto.

    - `docker image build -t {nome}:{tag} {contexto}` - Atribui um nome e uma *tag* ao resultado.
    - `docker image build -f {arquivo} {contexto}` - Escolhe outro Dockerfile.
    - `docker image build --no-cache {contexto}` - Refaz as etapas sem reutilizar os resultados guardados de construções anteriores.
    - `docker image build --pull {contexto}` - Tenta obter uma versão atualizada da imagem-base.

Para um exemplo completo, crie uma pasta `site`, coloque nela um arquivo `index.html` com o conteúdo desejado e salve o seguinte `Dockerfile` ao lado dessa pasta:

```dockerfile
FROM python:3.13-slim
WORKDIR /app
COPY site/ .
EXPOSE 8000
CMD ["python", "-m", "http.server", "8000", "--bind", "0.0.0.0"]
```

Esse exemplo utiliza o servidor HTTP incluído no Python para disponibilizar os arquivos de `/app`. Na pasta que contém o Dockerfile, construa e execute:

```bash
docker image build -t meu_site:1.0 .
docker container run --rm -d --name meu-site \
    -p 127.0.0.1:8000:8000 meu_site:1.0
```

Acesse `http://localhost:8000`. A opção `-p`, detalhada no capítulo 6, permite o acesso pelo computador. Se alterar os arquivos locais, construa novamente a imagem e substitua o *container*: os arquivos foram copiados durante a construção, não vinculados à pasta original. Neste exemplo, `docker container stop meu-site` também o remove por causa de `--rm`.

> `docker container commit {container} {imagem}` pode capturar alterações feitas nos arquivos de um *container*, mas não documenta os passos que produziram aquele estado nem inclui dados de volumes. Para manter a construção reproduzível, prefira o Dockerfile.

---

# 5. Armazenamento

## Escolher Onde Guardar os Dados:

Dados gravados apenas no *container* sobrevivem a uma parada, mas são perdidos quando ele é removido. Para separar os dados da vida do *container*, utilize uma montagem: um caminho dentro dele passa a acessar um armazenamento externo à sua camada gravável.

| Tipo | Quando Utilizar |
| --- | --- |
| **Volume** | Para dados persistentes gerenciados pelo Docker, como arquivos de um banco de dados. |
| ***Bind mount*** | Para compartilhar um arquivo ou diretório específico do computador, como código-fonte ou configurações. |

> `tmpfs` oferece armazenamento temporário em memória, descartado quando o *container* para. É uma alternativa para dados que não precisam ser persistidos.

## Gerenciar Volumes:

- `docker volume create {opções} {volume}` - Cria um volume. Se o nome for omitido, o Docker gera um identificador.

- `docker volume ls {opções}` - Lista os volumes existentes.

- `docker volume inspect {opções} {volumes}` - Mostra informações detalhadas sobre os volumes.

- `docker volume rm {opções} {volumes}` - Remove volumes que não estejam associados a *containers*, apagando seus dados.

Volumes com um nome escolhido, como `dados`, são chamados de **nomeados**. Os criados sem um nome escolhido são chamados de **anônimos**. Essa diferença afeta algumas operações de remoção, como `container rm -v`.

## Associar um Volume:

- `docker container run --mount type=volume,src={volume},dst={destino} {imagem}` - Disponibiliza um volume no caminho indicado dentro do *container*.

O exemplo abaixo grava um arquivo no volume e o lê usando outro *container*:

```bash
docker volume create dados
docker container run --rm --mount type=volume,src=dados,dst=/dados \
    alpine:3.22 sh -c 'echo "Teste de persistência" > /dados/teste.txt'
docker container run --rm --mount type=volume,src=dados,dst=/dados \
    alpine:3.22 cat /dados/teste.txt
```

Os dois *containers* são removidos ao terminar, mas o arquivo continua no volume `dados`. Para removê-lo definitivamente, utilize `docker volume rm dados` quando não precisar mais de seu conteúdo.

## Montar um Diretório do Computador:

- `docker container run --mount type=bind,src={origem},dst={destino} {imagem}` - Associa um caminho do computador a um caminho dentro do *container*. Na forma apresentada, a origem precisa existir.

No Bash, estando na pasta que contém o diretório `site` do capítulo anterior:

```bash
docker container run --rm -d --name site-local \
    -p 127.0.0.1:8080:80 \
    --mount type=bind,src="$PWD/site",dst=/usr/share/nginx/html,readonly \
    nginx:alpine
```

Acesse `http://localhost:8080`. `$PWD` representa a pasta atual; `readonly` permite ao *container* ler os arquivos, sem alterá-los por essa montagem. Edições feitas no computador ficam disponíveis para o servidor sem reconstruir a imagem.

A forma curta `-v {origem}:{destino}` também aceita volumes e diretórios. Um nome como `dados` indica um volume; um caminho absoluto indica um diretório do computador. Acrescentar `:ro` torna a montagem somente leitura.

> Em sistemas com SELinux, `:Z` e `:z` na opção `-v` ajustam os rótulos de acesso para uso privado ou compartilhado entre *containers*. Esses ajustes modificam os arquivos do hospedeiro e devem ser limitados à pasta necessária. Consulte a [documentação de bind mounts](https://docs.docker.com/engine/storage/bind-mounts/).

---

# 6. Redes e Portas

## Acessar a Aplicação pelo Computador:

A aplicação pode estar funcionando dentro do *container* sem estar disponível no navegador do computador. Para permitir esse acesso, publique sua porta ao criar o *container*.

- `docker container run -p {porta_host}:{porta_container} {imagem}` - Encaminha a porta do computador para a porta utilizada pela aplicação no *container*. Sem IP explícito, normalmente publica em todas as interfaces do hospedeiro.

- `docker container run -p 127.0.0.1:{porta_host}:{porta_container} {imagem}` - Limita a publicação ao próprio computador.

- `docker container port {container}` - Mostra as portas publicadas de um *container*.

Em `-p 127.0.0.1:8080:80`, o navegador acessa a porta **8080 do computador**, enquanto o servidor utiliza a porta **80 no container**. Outra aplicação não pode ocupar simultaneamente o mesmo endereço e porta do computador.

TCP é o protocolo padrão. Para UDP, acrescente `/udp`, como em `-p 5353:53/udp`. A instrução `EXPOSE` do Dockerfile apenas documenta a porta; não substitui `-p`.

## Comunicação entre *Containers*:

Uma rede *bridge* criada pelo usuário permite que os *containers* conectados se encontrem pelos nomes. Isso evita depender de endereços IP que podem mudar.

- `docker network create {opções} {rede}` - Cria uma rede; o tipo padrão é *bridge*.

- `docker network ls {opções}` - Lista as redes.

- `docker network inspect {opções} {redes}` - Mostra a configuração e os *containers* conectados.

- `docker network connect {opções} {rede} {container}` - Conecta um *container* existente à rede.

- `docker network disconnect {opções} {rede} {container}` - Desconecta um *container* da rede.

- `docker network rm {opções} {redes}` - Remove redes que não estejam em uso.

- `docker container run --network {rede} {imagem}` - Conecta o novo *container* à rede indicada.

```bash
docker network create minha-rede
docker container run -d --name web --network minha-rede nginx:alpine
docker container run --rm --network minha-rede alpine:3.22 \
    wget -qO- http://web:80
```

O último comando consulta o servidor pelo nome `web`. Não é necessário publicar a porta com `-p` para essa comunicação interna. Dentro de um *container*, `localhost` aponta para ele próprio, não para outro *container* nem para o computador.

---

# 7. *Docker Compose*

## Finalidade e Sintaxe:

O **Docker Compose** permite guardar a configuração de uma aplicação em um arquivo, em vez de repetir comandos longos de criação. Ele utiliza o Docker Engine para criar e gerenciar os *containers*, redes e volumes declarados.

| Elemento | Função |
| --- | --- |
| Dockerfile | Descreve como construir uma imagem. |
| `docker container run` | Cria um *container* com a configuração informada no terminal. |
| `compose.yaml` | Reúne a configuração dos serviços e recursos de uma aplicação. |

- `docker compose version {opções}` - Confere a instalação do Compose. Ele acompanha o Docker Desktop; no Engine instalado diretamente no Linux, pode ser necessário instalar o [plugin Compose](https://docs.docker.com/compose/install/linux/).

A sintaxe geral é `docker compose {opções_globais} {comando} {opções} {argumentos}`. As opções globais aparecem antes da operação; as específicas, depois:

- `docker compose -f {arquivo} {comando}` - Escolhe outro arquivo Compose.

- `docker compose -p {projeto} {comando}` - Define o nome do projeto, que agrupa os recursos da aplicação.

- `docker compose --env-file {arquivo} {comando}` - Escolhe o arquivo de variáveis utilizado para preencher valores no modelo Compose.

Em `docker compose -f compose.dev.yaml up -d`, `-f` é global e `-d` pertence a `up`.

## Primeiro Arquivo Compose:

Salve o seguinte conteúdo como `compose.yaml`, em uma pasta do projeto:

```yaml
services:
  web:
    image: nginx:alpine
    ports:
      - "127.0.0.1:8081:80"
```

`services` reúne os componentes da aplicação. `web` é o nome escolhido para este serviço, `image` seleciona a imagem e `ports` publica a porta. A indentação do YAML utiliza espaços e determina a relação entre os elementos.

Na mesma pasta, execute:

```bash
docker compose up -d
docker compose ps
```

A página padrão do Nginx ficará disponível em `http://localhost:8081`. O Compose cria uma rede para o projeto, e serviços dessa rede podem comunicar-se por seus nomes. Os comandos Compose recebem nomes de **serviços**, como `web`, mesmo quando o nome gerado para o *container* é diferente.

## Construir, Criar e Atualizar:

Nos comandos abaixo que aceitam `{serviços}`, a omissão seleciona todos os serviços aplicáveis à operação.

- `docker compose up {opções} {serviços}` - Obtém ou constrói as imagens conforme a configuração, cria ou atualiza os *containers* e os inicia. Sem opções, acompanha a saída no terminal; `Ctrl+C` interrompe a execução e para os serviços iniciados.

    - `docker compose up -d {serviços}` - Inicia em segundo plano.
    - `docker compose up --build {serviços}` - Constrói as imagens dos serviços com `build` antes de iniciar. Pode ser combinado com `-d`.

- `docker compose create {opções} {serviços}` - Cria os *containers* sem iniciá-los.

- `docker compose build {opções} {serviços}` - Constrói as imagens dos serviços que utilizam `build`, sem iniciar os *containers*.

- `docker compose pull {opções} {serviços}` - Baixa as imagens declaradas, sem substituir por si só os *containers* em execução.

Para utilizar um Dockerfile da pasta atual, o serviço pode declarar `build: .` no lugar de `image`. A publicação da porta precisa corresponder à aplicação: no exemplo Python do capítulo 4, a porta interna é 8000, não 80.

Depois de modificar o código de uma imagem própria, use `docker compose up -d --build`. Para atualizar uma imagem pronta, use `docker compose pull` e depois `docker compose up -d`. A recriação preserva os volumes montados, mas descarta dados que existiam apenas no *container* substituído.

## Consultar e Executar Comandos:

- `docker compose config {opções}` - Valida e mostra a configuração resultante, incluindo a substituição de variáveis.

- `docker compose ps {opções} {serviços}` - Lista os *containers* ativos do projeto.

    - `docker compose ps -a {serviços}` - Inclui os parados.

- `docker compose logs {opções} {serviços}` - Mostra a saída registrada dos serviços. `--tail {quantidade}` limita às últimas linhas.

    - `docker compose logs -f {serviços}` - Acompanha novas mensagens.

- `docker compose exec {opções} {serviço} {comando} {argumentos}` - Executa um processo em um *container* ativo do serviço. Os argumentos podem ser omitidos; a sessão já é interativa por padrão quando executada em um terminal.

- `docker compose run {opções} {serviço} {comando} {argumentos}` - Cria um *container* separado para uma tarefa pontual. O comando e seus argumentos podem ser omitidos para utilizar o padrão do serviço.

    - `docker compose run --rm {serviço} {comando} {argumentos}` - Remove o *container* dessa tarefa quando ela termina.

```bash
docker compose exec web sh
docker compose run --rm web nginx -t
```

A primeira linha abre um terminal no serviço já ativo. A segunda cria outro *container* para verificar a configuração do Nginx. `run` não publica por padrão as portas declaradas no serviço; `--service-ports` habilita essa publicação quando necessária.

## Volumes, Variáveis e Dependências:

Outros campos podem ser acrescentados ao serviço conforme a aplicação precisar:

| Campo | Finalidade |
| --- | --- |
| `volumes` | Associa volumes ou diretórios do computador. |
| `environment` | Define variáveis entregues à aplicação. |
| `env_file` | Carrega essas variáveis de um arquivo. |
| `command` e `entrypoint` | Substituem os padrões de execução da imagem. |
| `restart` | Define a política de reinicialização. |
| `depends_on` | Declara dependências entre serviços. |
| `healthcheck` | Define um comando para verificar se o serviço está saudável. |
| `networks` | Seleciona redes específicas em vez de apenas a rede padrão. |

Como outro exemplo, salve este `compose.yaml` em uma pasta de projeto separada. Ele define um Redis com volume nomeado e persistência habilitada:

```yaml
services:
  cache:
    image: redis:alpine
    command: ["redis-server", "--appendonly", "yes"]
    volumes:
      - dados_cache:/data

volumes:
  dados_cache:
```

O volume é declarado no nível superior e associado ao serviço em `/data`. Ele pode ser reutilizado quando o *container* for recriado. A aplicação também precisa estar configurada para gravar nesse caminho.

No primeiro exemplo, é possível substituir a publicação fixa por `"127.0.0.1:${APP_PORT:-8081}:80"`. O valor de `APP_PORT` será obtido do ambiente do terminal ou de um arquivo `.env`; se não estiver definido ou estiver vazio, será utilizado 8081.

```dotenv
APP_PORT=8082
```

**O `.env` preenche valores do arquivo Compose; não entrega automaticamente todas as variáveis aos containers.** Para entregá-las, utilize `environment` ou `env_file`. Arquivos com credenciais não devem ser versionados.

> `depends_on` sozinho controla a ordem de início, mas não garante que a aplicação dependente esteja pronta. Quando essa espera for necessária, combine `healthcheck` no serviço dependido com `condition: service_healthy` em sua dependência. Consulte o [controle da ordem de inicialização](https://docs.docker.com/compose/how-tos/startup-order/).

## Parar, Retomar e Remover:

- `docker compose stop {opções} {serviços}` - Para os *containers* sem removê-los. `-t {segundos}` ajusta a espera.

- `docker compose start {opções} {serviços}` - Retoma *containers* existentes que estão parados; não cria os ausentes.

- `docker compose restart {opções} {serviços}` - Reinicia os *containers*. Não aplica alterações feitas no arquivo Compose; para isso, utilize `up`.

- `docker compose down {opções}` - Para e remove os *containers* e as redes do projeto, preservando por padrão os volumes nomeados.

    - `docker compose down -v` - Também remove volumes nomeados declarados e volumes anônimos associados. Recursos declarados como externos não são removidos.

**`down -v` pode apagar os dados persistentes da aplicação.** Para apenas interromper o trabalho e retomá-lo depois, utilize `stop` e `start`.

---

# 8. Manutenção e Segurança

## Consultar e Liberar Espaço:

- `docker system df {opções}` - Mostra o espaço utilizado por imagens, *containers*, volumes e resultados de construção guardados em *cache*. `-v` apresenta detalhes.

- `docker system prune {opções}` - Remove *containers* parados, redes sem uso, imagens sem referência (*dangling*) e *cache* de construção não utilizado. Solicita confirmação.

    - `docker system prune -a` - Amplia a limpeza de imagens para todas as que não estejam associadas a *containers*.
    - `docker system prune --volumes` - Inclui volumes anônimos não utilizados na limpeza.

- `docker volume prune {opções}` - Remove volumes anônimos locais não utilizados por nenhum *container*. Solicita confirmação.

    - `docker volume prune -a` - Inclui também volumes nomeados não utilizados.

**“Não utilizado” não significa “sem importância”.** Um volume pode conter dados necessários mesmo sem estar associado a um *container*. Confira o alcance antes de confirmar; a opção `-f`, disponível nesses comandos, elimina a confirmação.

## Consultar Eventos:

- `docker system events {opções}` - Acompanha acontecimentos como criação, início, parada e remoção de *containers*.

Use `logs` para consultar a saída da aplicação e `events` para investigar operações realizadas no ambiente Docker. Para problemas de inicialização do próprio serviço, consulte o `journalctl` apresentado no capítulo 1.

## Cuidados de Acesso:

O isolamento de um *container* depende das permissões concedidas a ele. `--privileged` e a montagem de `/var/run/docker.sock` podem permitir controle amplo sobre o computador; não devem ser utilizados como solução genérica para erros de permissão.

Monte apenas os diretórios necessários e utilize somente leitura quando a aplicação não precisar gravar. Antes de executar configurações de terceiros, confira principalmente as montagens e os privilégios solicitados.

> Quando for necessário fixar exatamente o conteúdo de uma imagem, utilize um *digest*, no formato `imagem@sha256:{valor}`. Diferentemente de uma *tag*, ele identifica um conteúdo específico.

---

# Fontes

- DOCKER INC. *Docker Docs: Docker overview*. [S. l.], [s. d.]. Disponível em: [https://docs.docker.com/get-started/docker-overview/](https://docs.docker.com/get-started/docker-overview/). Acesso em: 15 set. 2026.
- DOCKER INC. *Docker Engine security: Rootless mode*. [S. l.], [s. d.]. Disponível em: [https://docs.docker.com/engine/security/rootless/](https://docs.docker.com/engine/security/rootless/). Acesso em: 15 set. 2026.
- DOCKER INC. *Docker CLI reference*. [S. l.], [s. d.]. Disponível em: [https://docs.docker.com/reference/cli/docker/](https://docs.docker.com/reference/cli/docker/). Acesso em: 15 set. 2026.
- DOCKER INC. *Dockerfile reference*. [S. l.], [s. d.]. Disponível em: [https://docs.docker.com/reference/dockerfile/](https://docs.docker.com/reference/dockerfile/). Acesso em: 15 set. 2026.
- DOCKER INC. *Storage*. [S. l.], [s. d.]. Disponível em: [https://docs.docker.com/engine/storage/](https://docs.docker.com/engine/storage/). Acesso em: 15 set. 2026.
- DOCKER INC. *Networking overview*. [S. l.], [s. d.]. Disponível em: [https://docs.docker.com/engine/network/](https://docs.docker.com/engine/network/). Acesso em: 15 set. 2026.
- DOCKER INC. *Docker Compose*. [S. l.], [s. d.]. Disponível em: [https://docs.docker.com/compose/](https://docs.docker.com/compose/). Acesso em: 15 set. 2026.
- DOCKER INC. *Set, use, and manage variables in a Compose file with interpolation*. [S. l.], [s. d.]. Disponível em: [https://docs.docker.com/compose/how-tos/environment-variables/variable-interpolation/](https://docs.docker.com/compose/how-tos/environment-variables/variable-interpolation/). Acesso em: 15 set. 2026.
